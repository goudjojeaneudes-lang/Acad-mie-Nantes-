exports.handler = async (event) => {
  const { ADMIN_PASSWORD, NETLIFY_TOKEN, SITE_ID } = process.env;
  const h = { 'Content-Type': 'application/json' };
  if (!ADMIN_PASSWORD || !NETLIFY_TOKEN || !SITE_ID)
    return { statusCode: 500, headers: h, body: JSON.stringify({ error: 'Variables d’environnement manquantes' }) };
  if ((event.headers['x-admin-password'] || '') !== ADMIN_PASSWORD)
    return { statusCode: 401, headers: h, body: JSON.stringify({ error: 'Mot de passe incorrect' }) };
  try {
    let all = [];
    for (let page = 1; page <= 10; page++) {
      const r = await fetch(`https://api.netlify.com/api/v1/sites/${SITE_ID}/submissions?per_page=100&page=${page}`,
        { headers: { Authorization: `Bearer ${NETLIFY_TOKEN}` } });
      if (!r.ok) throw new Error('API ' + r.status);
      const items = await r.json();
      all = all.concat(items.filter(i => i.form_name === 'candidature'));
      if (items.length < 100) break;
    }
    const rows = all.map(i => ({ id: i.id, date: i.created_at, ...i.data }));
    return { statusCode: 200, headers: h, body: JSON.stringify(rows) };
  } catch (e) {
    return { statusCode: 502, headers: h, body: JSON.stringify({ error: 'Lecture impossible : ' + e.message }) };
  }
};
