# Pharmaceutical MIS

A full‑stack Pharmaceutical Management Information System.

- Backend: Django 5 + DRF, JWT auth, Postgres/SQLite, Jazzmin admin theme.
- Frontend: React 18 + Vite + TypeScript, TailwindCSS, shadcn/ui, TanStack Query, React Router.

## Project Overview — What This Project Does

This Pharmaceutical Management Information System streamlines pharmacy operations with a secure backend API and a modern web UI.

- **Authentication & Users** (`server/core/`)
  - Secure JWT authentication (login, refresh). Registration and current user profile endpoints.
  - Admin/permissions extensible via Django admin and DRF.

- **Medicines Catalog** (`server/medicines/`)
  - Maintain a master list of medicines with CRUD endpoints (name, codes, pricing, metadata).
  - Search, ordering, and filtering via DRF + `django-filter`.

- **Inventory Management** (`server/stock/`)
  - Track on‑hand stock per medicine; support receipts/adjustments.
  - Basis for low‑stock checks and movement history.

- **Sales Processing** (`server/sales/`)
  - Record sales with line items linked to medicines; decrements stock on sale.
  - Retrieve sales lists/details for operations.

- **Suppliers Management** (`server/suppliers/`)
  - Manage supplier records and contact details; link to purchasing/receipts.

- **Reports & Insights** (`server/reports/`)
  - Endpoints for operational summaries (e.g., sales summaries, inventory snapshots).
  - Frontend visualizations powered by `recharts`.

- **Admin Panel**
  - Jazzmin‑themed Django admin for quick back‑office data management.

- **REST API**
  - Namespaced endpoints under `api/*`:
    - `api/auth/` → `register/`, `login/`, `refresh/`, `me/`, `users/count/`
    - `api/medicines/`, `api/stock/`, `api/sales/`, `api/suppliers/`, `api/reports/`
  - Health check at `/` returns API status JSON.

- **Frontend App** (`client/`)
  - React + Vite + TypeScript for dashboards, lists, forms, reports.
  - Token storage and one‑time auto‑refresh on 401 in `client/src/lib/api.ts`.
  - Modern UI with shadcn/ui and Radix UI; data fetching with TanStack Query.

- **Config & Deployment**
  - Environment‑driven configuration for DB/JWT/hosts (backend) and `VITE_API_BASE_URL` (frontend).
  - Production: Postgres, `DJANGO_DEBUG=0`, secure secret key, reverse proxy; serve static files and built frontend.

## Core Workflows

- **Onboarding**
  - Admin creates a superuser (`python manage.py createsuperuser`) and configures users.
  - Users register/login and receive JWTs to access the app.

- **Daily Operations**
  - Maintain medicines catalog; update stock via receipts/adjustments.
  - Record sales; inventory decrements accordingly.
  - Review reports for sales trends and inventory positions.

- **Administration**
  - Use Django admin for bulk edits, permissions, and oversight.
  - Configure environment variables and database per environment.

## Repository Structure

- `server/` — Django backend
  - Settings: `server/pharma_backend/settings.py`
  - URLs: `server/pharma_backend/urls.py`
  - Apps: `server/core/`, `server/medicines/`, `server/stock/`, `server/sales/`, `server/suppliers/`, `server/reports/`
  - Entrypoint: `server/manage.py`
  - Dependencies: `server/requirements.txt`
- `client/` — React frontend
  - Scripts: `client/package.json`
  - API helper: `client/src/lib/api.ts` (uses `VITE_API_BASE_URL`)
  - Components/pages/hooks in `client/src/...`

## Features

- **Auth & Users** (`server/core/`)
  - JWT login/refresh, registration, current user, user count.
  - Endpoints in `server/core/urls.py`.
- **Domain modules**
  - Medicines, Stock, Sales, Suppliers, Reports with REST endpoints.
- **Admin Panel**
  - Jazzmin‑themed Django admin.
- **API**
  - Filtering, search, ordering (`django-filter`, DRF filters).
  - Health check at `/`.

## Requirements

- Python 3.11+
- Node.js 18+ (PNPM or npm)
- PostgreSQL 14+ (or SQLite for local/dev)
- Windows supported

## Backend Setup (Django)

1. Create and activate a virtualenv:
   - PowerShell
     ```
     cd server
     python -m venv .venv
     .\.venv\Scripts\Activate.ps1
     ```
   - CMD
     ```
     cd server
     python -m venv .venv
     .\.venv\Scripts\activate.bat
     ```

2. Install dependencies:
   ```
   pip install -r requirements.txt
   ```

3. Create `server/.env`:
   ```
   # Django
   DJANGO_SECRET_KEY=change-me
   DJANGO_DEBUG=1
   DJANGO_ALLOWED_HOSTS=localhost,127.0.0.1

   # Database
   # Use 'sqlite' for zero-config local dev, or 'postgres' for Postgres.
   DB_ENGINE=sqlite
   # For Postgres (set DB_ENGINE=postgres):
   POSTGRES_DB=pharma_db
   POSTGRES_USER=postgres
   POSTGRES_PASSWORD=postgres
   POSTGRES_HOST=localhost
   POSTGRES_PORT=5432

   # JWT
   JWT_ACCESS_MINUTES=30
   JWT_REFRESH_DAYS=7
   ```

4. Migrate and create superuser:
   ```
   python manage.py migrate
   python manage.py createsuperuser
   ```

5. Run the server:
   ```
   python manage.py runserver
   ```
   - API root/health: http://localhost:8000/
   - Admin: http://localhost:8000/admin/

## Frontend Setup (React + Vite)

1. Install dependencies:
   ```
   cd client
   pnpm install
   # or: npm install
   ```

2. Create `client/.env`:
   ```
   VITE_API_BASE_URL=http://localhost:8000
   ```
   Used by `client/src/lib/api.ts` (defaults to `http://localhost:8000` if not set).

3. Start dev server:
   ```
   pnpm dev
   # or: npm run dev
   ```
   - Vite: http://localhost:5173

## Authentication

- Endpoints (`server/core/urls.py`):
  - `POST /api/auth/register/`
  - `POST /api/auth/login/`
  - `POST /api/auth/refresh/`
  - `GET /api/auth/me/`
  - `GET /api/auth/users/count/`

- Frontend token handling (`client/src/lib/api.ts`):
  - Stores `access_token` and `refresh_token` in localStorage.
  - Auto‑refreshes access token once on 401 via `/api/auth/refresh/`.

## API Namespaces

- Aggregated in `server/pharma_backend/urls.py`:
  - `api/medicines/`
  - `api/stock/`
  - `api/sales/`
  - `api/suppliers/`
  - `api/reports/`

## CORS

- Enabled for development (`CORS_ALLOW_ALL_ORIGINS = True` in `server/pharma_backend/settings.py`).
- Adjust for production as needed.

## Environment Variables

- Backend (`server/.env`):
  - `DJANGO_SECRET_KEY`
  - `DJANGO_DEBUG`
  - `DJANGO_ALLOWED_HOSTS` (comma‑separated)
  - `DB_ENGINE` = `sqlite` or `postgres`
  - For Postgres: `POSTGRES_DB`, `POSTGRES_USER`, `POSTGRES_PASSWORD`, `POSTGRES_HOST`, `POSTGRES_PORT`
  - `JWT_ACCESS_MINUTES`, `JWT_REFRESH_DAYS`

- Frontend (`client/.env`):
  - `VITE_API_BASE_URL` (default `http://localhost:8000`)

## Scripts

- Backend:
  - `python manage.py runserver`
  - `python manage.py migrate`
  - `python manage.py createsuperuser`

- Frontend (`client/package.json`):
  - `pnpm dev` — start Vite dev server
  - `pnpm build` — production build
  - `pnpm preview` — preview built app
  - `pnpm lint` — lint

## Production Notes

- Set `DJANGO_DEBUG=0` and a strong `DJANGO_SECRET_KEY`.
- Configure `DJANGO_ALLOWED_HOSTS`.
- Prefer Postgres in production (`DB_ENGINE=postgres`).
- Serve static files properly (e.g., WhiteNoise or web server).
- Run with a WSGI/ASGI server (e.g., gunicorn/uvicorn) behind a reverse proxy.
- Build frontend and serve static bundle or via a proxy to the API.

## Health Check

- `GET /` returns:
  ```
  { "status": "ok", "name": "Pharm MIS API" }
  ```

## Troubleshooting

- Authentication: Ensure `VITE_API_BASE_URL` points to backend and `/api/auth/*` is reachable.
- CORS: Configure for non‑local origins in production.
- Database: Verify Postgres credentials/host/port and that the service is running.
- Windows venv activation: Use PowerShell `Activate.ps1` or CMD `activate.bat` as shown above.

## License

Specify your license here (e.g., MIT). If unsure, add a LICENSE file at repo root.
