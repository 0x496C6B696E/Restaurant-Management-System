# Restaurant Management System

A REST API backend for running a restaurant: menu, orders, tables, inventory, staff accounts, customer feedback, reports and ads. It is built with Django REST Framework and JWT auth, and Celery workers on Redis handle background jobs.

## Architecture

```
                ┌──────────────┐
  client ──────▶│ Nginx        │
                └──────┬───────┘
                       ▼
                ┌──────────────┐        ┌──────────────┐
                │ Django + DRF │──────▶ │ PostgreSQL   │  (SQLite in dev)
                │ (gunicorn)   │        └──────────────┘
                └──────┬───────┘
                       │ tasks
                       ▼
                ┌──────────────┐        ┌──────────────┐
                │ Redis broker │◀──────▶│ Celery worker│
                └──────────────┘        │ + Celery beat│
                                        └──────────────┘
```

The backend is split into 8 Django apps under `backend/apps/`:

| App | Endpoint prefix | Responsibility |
|---|---|---|
| `users` | `/api/users/` | Accounts, roles, JWT login |
| `menu` | `/api/menu/` | Categories and menu items |
| `orders` | `/api/orders/` | Orders and order items, plus scheduled email tasks |
| `tables` | — | Restaurant tables |
| `inventory` | `/api/inventory/` | Stock tracking |
| `feedback` | `/api/feedback/` | Customer reviews |
| `reports` | `/api/reports/` | Daily reports |
| `ads` | `/api/ads/` | Promotions and ads |

Endpoint details are in [docs/API.md](docs/API.md).

## Tech stack

Python · Django · Django REST Framework · SimpleJWT · Celery + django-celery-beat · Redis · PostgreSQL · gunicorn · WhiteNoise · Docker · Nginx

## Run locally

```bash
cd backend
pip install -r requirements.txt
python manage.py migrate          # uses core.settings.dev (SQLite)
python manage.py runserver
```

With Docker (web + Celery worker + beat + Redis):

```bash
cd backend
docker compose up --build
```

## Tests

```bash
cd backend
pytest
```

Tests are in `backend/test/` and cover users, menu, orders and inventory.

## Infrastructure

`infrastructure/` contains deployment sketches: Nginx config, Kubernetes manifests, a Prometheus config, Terraform, and CI pipelines (GitHub Actions and Jenkins).

## Roadmap

- [ ] Add PostgreSQL to `docker compose` so a single `docker compose up` brings up the whole stack
- [ ] Serve Swagger/OpenAPI docs (drf-yasg) and add a screenshot here
- [ ] Expose the `tables` app's routes
- [ ] Report test coverage in CI and add a badge
- [ ] Tag the `v1.0` release

## License

[Apache-2.0](LICENSE)
