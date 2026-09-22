# رواتب PerfectScan — Payroll & Daily Expenses

A small internal tool for PerfectScan radiology center: track staff and
reporting doctors, calculate each person's net monthly pay automatically
(fixed monthly salary for staff; per-shift pay or a percentage of monthly
case value for doctors, set per doctor), and log daily expenses (damaged
equipment, paper, supplies, maintenance, utilities, other).

Built with Next.js (App Router, TypeScript) and Postgres.

## Project structure

```
app/
  layout.tsx, globals.css, page.tsx   – app shell, RTL Arabic
  api/employees, api/payroll,
  api/expenses, api/health            – REST route handlers (CRUD)
components/                           – React UI (Dashboard, Employees,
                                         Payroll, Expenses, modals, toasts)
lib/
  db.ts     – all SQL queries, one place
  calc.ts   – net-pay calculation, shared by the UI
  types.ts  – shared TypeScript types
db/schema.sql                         – table definitions
scripts/migrate.mjs                   – runs schema.sql against your DB
```

## 1. Local setup

```bash
npm install
cp .env.example .env        # then edit .env with your Postgres connection string
npm run db:migrate          # creates the employees / payroll_entries / expenses tables
npm run dev                 # http://localhost:3000
```

If `npm run db:migrate` fails (some hosted providers restrict
multi-statement queries), open `db/schema.sql` and run it once in your
provider's SQL editor instead (Neon, Supabase and the Vercel Postgres
dashboard all have one built in).

Any Postgres works — a local instance, or a free Neon/Supabase database
for local dev. In production on Vercel, see below.

## 2. Deploying on Vercel

1. **Push this repository to GitHub** (see the next section if you need
   help with that), then in the Vercel dashboard: **Add New… → Project**
   and import the repo. Vercel auto-detects Next.js — no build settings
   to change.
2. **Add a Postgres database.** In your new Vercel project: **Storage →
   Create Database → Postgres** (powered by Neon). Creating it from
   inside the project automatically sets the `DATABASE_URL` environment
   variable (and a few related ones) for you — nothing to copy/paste.
   (You can instead use an external Postgres — Neon, Supabase, Railway,
   etc. — and add its connection string yourself as the `DATABASE_URL`
   environment variable in **Project Settings → Environment Variables**.)
3. **Run the migration once against that database** — easiest is to open
   the database's SQL editor (from the Vercel Storage tab, or your
   provider's dashboard) and paste in the contents of `db/schema.sql`.
   Alternatively, run `npm run db:migrate` locally with `DATABASE_URL`
   in your `.env` pointed at the same production database.
4. **Deploy.** Vercel builds and deploys automatically on every push to
   the main branch. After it's live, open `/api/health` on your deployed
   URL — `{"ok":true,"database":"connected"}` confirms the app can reach
   the database.

### Environment variables you need on Vercel

| Variable       | Required | Notes |
|----------------|----------|-------|
| `DATABASE_URL` | Yes      | Set automatically if you create the database from inside the Vercel project (step 2 above). Set it yourself (in **Project Settings → Environment Variables**) if you're using an external Postgres provider. `POSTGRES_URL` also works as a fallback name if that's what your provider's integration sets. |

That's the only one — there are no API keys or secrets beyond the
database connection string.

## 3. Pushing to GitHub

```bash
git init
git add .
git commit -m "Initial commit"
git branch -M main
git remote add origin https://github.com/<your-username>/<repo-name>.git
git push -u origin main
```

(Create the empty repository on GitHub first — github.com/new — without
a README/.gitignore, since this project already has both.)
