# MALI MODE — Vercel + Supabase

MALI MODE is a FastAPI web app for youth entrepreneurs and small businesses. The frontend templates and static assets are served by the FastAPI application. Supabase provides hosted PostgreSQL; Vercel runs the Python app as a serverless function.

## Repository structure

```text
MALI_MODE_Vercel_Supabase/
├── api/
│   └── index.py              # Vercel entry point
├── app/
│   ├── main.py               # FastAPI routes and application
│   ├── database.py           # SQLAlchemy/Supabase PostgreSQL connection
│   ├── models.py
│   ├── security.py
│   ├── mailer.py
│   ├── plan.py
│   ├── templates/            # HTML pages
│   └── static/               # CSS and JavaScript
├── supabase/
│   └── migrations/           # SQL migration files
├── tests/
├── requirements.txt
├── vercel.json
├── .env.example
├── .gitignore
└── README.md
```

## Deploy to GitHub, Vercel and Supabase

1. Create a Supabase project. In **Project Settings → Database** or **Connect**, copy the PostgreSQL connection string. Use the Session Pooler URL if your deployment environment cannot connect over IPv6. Keep the database password private.
2. In Supabase, open **SQL Editor** and run `supabase/migrations/20261017_founder_trade_formalisation.sql` if required by your schema. The application also creates its SQLAlchemy tables at startup; the migration adds the additional supplier/order/document tables.
3. Upload the *contents* of this project folder to the root of a GitHub repository. The root must directly contain `api/`, `app/`, `requirements.txt`, and `vercel.json` — do not upload the outer folder as a nested folder.
4. In Vercel, choose **Add New → Project**, import that GitHub repository, and deploy. Leave the project root directory set to `./` (repository root).
5. In Vercel → **Project → Settings → Environment Variables**, add:
   - `DATABASE_URL` = your Supabase PostgreSQL connection string
   - `SECRET_KEY` = a long, random secret
   - `BASE_URL` = your deployed Vercel HTTPS URL, e.g. `https://your-project.vercel.app`
   - `COOKIE_SECURE` = `true`
   - `SMTP_HOST`, `SMTP_PORT`, `SMTP_USERNAME`, `SMTP_PASSWORD`, `SMTP_FROM`, `SMTP_TLS` = your email provider's SMTP settings, if using email verification
   - `OPENAI_API_KEY` and `OPENAI_MODEL` only if you intentionally use that optional feature
6. Redeploy after setting environment variables. Visit `/health` on the deployed URL to check the app starts. Test signup, email verification, sign-in, saving a transaction, and persistence after refresh.

## Important notes

- Never commit `.env`, database passwords, API keys, or real credentials. `.env.example` contains placeholders only.
- Vercel is serverless: do not rely on local SQLite or local files for persistent storage. Set `DATABASE_URL` to Supabase before testing.
- Keep the Supabase database URL on the server only. Do not expose it in browser JavaScript.
- Email verification requires working SMTP settings. Without SMTP, the current development fallback logs verification links to server logs and is not suitable for a public production deployment.
- Vercel serverless functions have execution and connection limits. This is a prototype deployment setup; test database connection behaviour and all flows after deployment.
- This app's insights are rule-based and based on the records entered; do not present them as audited accounts or guaranteed funding outcomes.

## Local development

Use Python 3.11 or newer:

```bash
python -m venv .venv
# Windows: .venv\Scripts\activate
# macOS/Linux: source .venv/bin/activate
pip install -r requirements.txt
cp .env.example .env
uvicorn app.main:app --reload
```

Open `http://127.0.0.1:8000`. API docs: `/docs`; health check: `/health`.
