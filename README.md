# Kittygram

Social network for cat owners: a user registers, adds cats with a photo, colour, birth year and a list of achievements, and browses other users' cats.

Built during the *Python Developer* course at Yandex Practicum (2025–2026). Every project was reviewed and accepted by a course mentor. This repository is the deployment stage of the course: the application is containerised and shipped through a GitHub Actions pipeline.

## Architecture

| Container | Role |
|---|---|
| `backend` | Django + Django REST Framework, Gunicorn |
| `frontend` | React SPA, built into static files |
| `gateway` | Nginx — serves static and media files, proxies `/api/` and `/admin/` to the backend |
| `db` | PostgreSQL |

## Tech stack

Python 3.12 · Django · Django REST Framework · Djoser (token auth) · PostgreSQL · Gunicorn · Nginx · Docker Compose · GitHub Actions · React

## API

- `GET/POST /api/cats/`, `GET/PUT/PATCH/DELETE /api/cats/<id>/` — cats (image upload as base64)
- `GET/POST /api/achievements/` — achievements
- `/api/users/`, `/api/token/login/` — registration and token auth (Djoser)

## Run locally with Docker

```bash
cp .env.example .env            # POSTGRES_*, DB_HOST, DB_PORT, SECRET_KEY, ALLOWED_HOSTS
docker compose up -d --build
docker compose exec backend python manage.py migrate
docker compose exec backend python manage.py collectstatic --noinput
```

The app is available at `http://localhost:9000`.

## Tests

```bash
pip install -r backend/requirements.txt flake8
flake8 backend --exclude=backend/*/migrations --ignore=E501,W503
cd backend && python manage.py test
```

## CI/CD

`.github/workflows/main.yml`:

1. On every push to `main` — backend flake8 and Django tests, frontend tests.
2. On manual run (`workflow_dispatch`) — build and push backend, frontend and gateway images to Docker Hub, copy `docker-compose.production.yml` to the server via SSH, pull images, restart, run migrations and collect static; a Telegram message confirms the deployment (`TELEGRAM_TOKEN`, `TELEGRAM_TO` secrets).

`kittygram_workflow.yml` in the repository root is a copy of the workflow kept for the course checker.

## Author

Roman Tanashkin — [github.com/RomanTanashkin](https://github.com/RomanTanashkin)
