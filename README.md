# SupportOps API

A portfolio-ready customer-support ticketing backend built with **FastAPI, SQLAlchemy 2, Pydantic, JWT authentication, role-based access, SLA calculation, audit events, pytest, Docker, PostgreSQL, and GitHub Actions**.

This is a senior-style portfolio sample, not a claim of production deployment. Review, run, test, and extend it yourself so you can confidently explain every design choice in interviews.

## Features
- Customer registration and JWT login
- Roles: customer, agent, admin
- Ticket creation, search/filter, pagination, assignment and status workflow
- Priority-based SLA due times (critical 1h, high 4h, medium 24h, low 72h)
- Comments and audit timeline
- SQLAlchemy data layer; SQLite by default and PostgreSQL via Docker
- API docs at `/docs`, tests, linting and CI workflow

## Run locally (Python 3.11+)
```powershell
python -m venv .venv
.\.venv\Scripts\Activate.ps1
python -m pip install --upgrade pip
pip install -e ".[dev]"
Copy-Item .env.example .env
uvicorn app.main:app --reload
```
Open `http://127.0.0.1:8000/docs`.

## Demo
1. `POST /api/v1/auth/register` with `full_name`, `email`, `password` (minimum 10 characters).
2. `POST /api/v1/auth/login` using form fields `username` (your email) and `password`.
3. Click **Authorize** in Swagger and provide the returned bearer token.
4. Create a ticket, list it, add a comment, then view `/api/v1/tickets/{id}/events`.

New accounts register as `customer`. To test staff-only operations, promote a test user to `agent` in the local database using a DB client or add a secure admin bootstrap process. Do not expose role promotion publicly.

## Run checks
```powershell
pytest
ruff check .
ruff format --check .
```

## Docker / PostgreSQL
```powershell
docker compose up --build
```
Then visit `http://localhost:8000/docs`.

## Architecture
`Router → Pydantic validation → service/business rules → SQLAlchemy ORM → database`

See `docs/ARCHITECTURE.md` and `docs/GITHUB_SETUP.md`. For production, replace startup `create_all` with Alembic migrations, add rate limiting/observability, secret management, refresh-token revocation, background jobs and a business-hours-aware SLA calendar.
