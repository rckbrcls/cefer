# CEFER Management Server

> Status: archived academic coursework backend.

This package contains the Flask API, PostgreSQL SQL assets, schema bootstrap code, synthetic data generators, Docker runtime files, and database helper scripts for CEFER Management.

## Responsibilities

- Expose Flask API routes for authentication, administration, internal users, staff users, external invitees, reports, SQL views, extension groups, and debug database helpers.
- Maintain request-scoped PostgreSQL sessions.
- Apply schema, functions, triggers, indexes, views, and seed data.
- Execute SQL query files from `server/sql/queries/`.
- Provide synthetic data generation for coursework demonstrations.

## Stack

- Python 3.12.
- Flask.
- Flask-Cors.
- psycopg2.
- Faker.
- PostgreSQL SQL files.
- Docker.

## Structure

```text
server/
|-- app/
|   |-- __init__.py          # Flask app factory
|   |-- config.py            # Environment-backed config
|   |-- database.py          # DBSession wrapper
|   |-- extensions.py        # CORS and request database session setup
|   |-- routes/              # Flask blueprints grouped by domain
|   `-- services/            # Auth decorators, SQL executor, bootstrap helpers
|-- data_generators/         # Synthetic data generation modules
|-- docker/
|   |-- Dockerfile           # Flask container image
|   `-- entrypoint.py        # PostgreSQL wait and optional population flow
|-- scripts/
|   |-- populate_db.sh       # Manual population helper
|   `-- downgrade_db.sh      # Manual cleanup helper
|-- sql/
|   |-- functions/           # PL/pgSQL functions and triggers
|   |-- queries/             # Route-level SQL files
|   |-- downgrades/          # Data cleanup SQL files
|   |-- upgrade_schema.sql   # Main schema
|   |-- downgrade_schema.sql # Schema cleanup
|   |-- indexes.sql          # Explicit indexes
|   `-- views.sql            # SQL views
|-- requirements.txt
`-- wsgi.py
```

## Flask Application Flow

`server/app/__init__.py` creates the application through `create_app()`:

1. Create a Flask app.
2. Load `AppConfig`.
3. Register Flask-Cors and database session hooks.
4. Register route blueprints.

The WSGI entrypoint is `server/wsgi.py`.

## Route Groups

| Blueprint | Prefix | Purpose |
| --- | --- | --- |
| `auth_blueprint` | `/auth` | Internal login, registration, session user, password, external login |
| `admin_blueprint` | `/admin` | Admin dashboard and management endpoints |
| `internal_blueprint` | `/internal` | Internal user activities, reservations, invites, equipment |
| `staff_blueprint` | `/staff` | Staff activities, participants, reservations |
| `external_blueprint` | `/external` | External invite dashboard and accept/reject actions |
| `extension_group_blueprint` | `/extension_group` | Extension group management |
| `reports_blueprint` | `/reports` | Analytical report data |
| `views_blueprint` | `/views` | API access to SQL view data |
| `debug_blueprint` | `/debug` | Database status, population, and cleanup helpers |
| `home_blueprint` | `/` | Basic root endpoint |

## Database Access

`server/app/database.py` defines `DBSession`, a small wrapper around psycopg2 connections.

`server/app/extensions.py` creates a database session for each request and closes it during teardown. For non-debug requests, it calls `ensure_schema_populated()` from `server/app/services/database/bootstrap.py`.

Most routes use `server/app/services/database/executor.py` to load SQL from `server/sql/queries/` and serialize PostgreSQL date/time values for JSON responses.

## SQL Assets

Main files:

- `sql/upgrade_schema.sql`
- `sql/downgrade_schema.sql`
- `sql/indexes.sql`
- `sql/views.sql`

Function files:

- `sql/functions/auth_functions.sql`
- `sql/functions/admin_functions.sql`
- `sql/functions/internal_functions.sql`
- `sql/functions/staff_functions.sql`
- `sql/functions/common_triggers.sql`

Query files are grouped under `sql/queries/` by API/domain.

## Data Generation

Synthetic data generation is coordinated by:

- `data_generators/populate.py`
- `data_generators/data_generator.py`

Domain generator folders:

- `pessoas/`
- `infraestrutura/`
- `reservas/`
- `atividades/`
- `eventos/`
- `grupos/`
- `auth/`

Seeded accounts and an external token are documented in `../LOGINS.md`.

## Environment

The backend reads database and Flask settings from environment variables:

- `DB_HOST`
- `DB_PORT`
- `DB_NAME`
- `DB_USER`
- `DB_PASSWORD`
- `DB_SCHEMA`
- `FLASK_SECRET_KEY`
- `FLASK_DEBUG`
- `FLASK_RUN_HOST`
- `FLASK_RUN_PORT`
- `CORS_ORIGINS`
- `POPULATE_DB`

See `../.env.example` and `../docs/setup.md`.

## Scripts

```bash
./scripts/populate_db.sh
./scripts/downgrade_db.sh
```

These scripts set `PYTHONPATH` to the server root and call the Python data generator or downgrade entrypoint.

## Docker

`docker/Dockerfile` builds the Flask container. `docker/entrypoint.py` waits for PostgreSQL, optionally runs database population when `POPULATE_DB=true`, and starts Flask.

The root `docker-compose.yml` is the main orchestration entrypoint.

## Notes

- Debug routes can mutate database state and should not be public.
- Some runtime messages and comments remain in Portuguese; this documentation refresh did not change application code.
- No automated backend test setup was identified.
- This server is archived coursework and should be revalidated before reuse.
