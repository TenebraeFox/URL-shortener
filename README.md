<div align="center">

# slfox-shortener · Flask + FastAPI + PostgreSQL

URL shortener for **slfox.ru** with modern Tailwind UI, user accounts, API tokens, link expiry and admin panel.

[![License: MIT](https://img.shields.io/badge/License-MIT-green.svg)](LICENSE)
[![Python](https://img.shields.io/badge/python-3.11+-blue.svg)]()
[![FastAPI](https://img.shields.io/badge/FastAPI-🚀-informational)]()
[![Flask](https://img.shields.io/badge/Flask-UI-lightgrey)]()

</div>

## Features

- ✨ **Modern UI** — Tailwind CSS (CDN), clean dashboard & toasts
- 👤 **Accounts** — register/login, hashed passwords
- 🔑 **API tokens** — visible in dashboard, **regenerate** anytime
- 🔗 **Unique slugs** — custom or random
- ⏳ **Expiry** — 1 day / 1 month / 1 year / never
- 📈 **Clicks counter**
- 🧰 **Admin panel** — users & links overview, toggle admin, delete users
- 🐘 **PostgreSQL** via `docker-compose`
- 🚀 **Two services** — Flask (site+redirect) and FastAPI (API)

## Quickstart

```bash
git clone https://github.com/your-github/slfox-shortener.git
cd slfox-shortener
pip install -r requirements.txt

cp .env.example .env
# edit .env: SECRET_KEY, BASE_URL=https://slfox.ru, DATABASE_URL=...

docker-compose up -d
python scripts/create_tables.py
python scripts/upgrade_001_add_columns.py

# Run dev servers
bash run_flask.sh   # http://127.0.0.1:5000
bash run_api.sh     # http://127.0.0.1:8000/docs
```

## Environment

```env
DATABASE_URL=postgresql+psycopg2://slfox:slfox_password@localhost:5432/slfox
SECRET_KEY=change-this
DOMAIN=slfox.ru
BASE_URL=https://slfox.ru
```

## API (FastAPI)

Auth via header: `X-API-Token: <your token>`

- `GET /me` → returns token info
- `GET /links` → list your links
- `POST /links` → create link
  ```json
  { "target_url": "https://example.com", "slug": "optional", "ttl": "1d|1m|1y|never" }
  ```
- `DELETE /links/{slug}` → remove your link

## Admin

- Visit `/admin` as user with `is_admin=true`.
- Actions: toggle admin, delete user (cascade deletes links).

## Nginx (example)

```nginx
server {
  server_name slfox.ru;
  location / {
    proxy_pass http://127.0.0.1:5000;
    proxy_set_header Host $host;
    proxy_set_header X-Forwarded-For $proxy_add_x_forwarded_for;
  }
}
server {
  server_name api.slfox.ru;
  location / {
    proxy_pass http://127.0.0.1:8000;
    proxy_set_header Host $host;
    proxy_set_header X-Forwarded-For $proxy_add_x_forwarded_for;
  }
}
```

## Security Notes

- Use strong `SECRET_KEY`, secure cookies, TLS in production.
- Treat API tokens as secrets; regeneration invalidates previous token.
- Consider rate limiting and CSRF protection for form POSTs behind a proxy/WAF.

## Roadmap

- Custom domains per user
- QR codes
- Click analytics/UTM breakdown
- Bulk import/export CSV
- OAuth login providers

## License

MIT — see `LICENSE`.
