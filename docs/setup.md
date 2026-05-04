# Setup

This guide explains how the archived CEFER Management stack is configured. Commands are documented for users of the repository; this documentation refresh did not execute build, dev, Docker, or test commands.

## Prerequisites

For the Docker Compose path:

- Docker
- Docker Compose

For manual local development:

- Node.js 20+
- pnpm
- Python 3.12+
- PostgreSQL 17 or a reachable Compose PostgreSQL service

## Environment File

Create `.env` from the template:

```bash
cp .env.example .env
```

Review the values before starting anything.

### PostgreSQL Variables

| Variable | Description |
| --- | --- |
| `POSTGRES_USER` | PostgreSQL container user |
| `POSTGRES_PASSWORD` | PostgreSQL container password |
| `POSTGRES_DB` | PostgreSQL database name |
| `POSTGRES_PORT` | Host port mapped to container port `5432` |

### Flask Database Variables

| Variable | Description |
| --- | --- |
| `DB_HOST` | Use `postgres` in Docker Compose, or `localhost` for manual local database access |
| `DB_PORT` | PostgreSQL port from the Flask perspective |
| `DB_NAME` | Database name |
| `DB_USER` | Database user |
| `DB_PASSWORD` | Database password |
| `DB_SCHEMA` | Optional PostgreSQL schema search path |

### Flask Runtime Variables

| Variable | Description |
| --- | --- |
| `FLASK_SECRET_KEY` | Flask session signing secret |
| `FLASK_DEBUG` | Enables Flask debug mode when `true` |
| `FLASK_RUN_HOST` | Flask bind host inside the runtime |
| `FLASK_RUN_PORT` | Flask bind port inside the runtime |
| `FLASK_PORT` | Host port mapped to the Flask service |
| `POPULATE_DB` | Runs the database seed flow in the Flask container when `true` |

### Frontend And CORS Variables

| Variable | Description |
| --- | --- |
| `NEXT_PUBLIC_API_URL` | Browser-visible Flask API URL |
| `NEXTJS_PORT` | Host port mapped to the Next.js service |
| `NODE_ENV` | Node environment |
| `CORS_ORIGINS` | Comma-separated list of allowed browser origins |

## Docker Compose Setup

The root `docker-compose.yml` starts:

- `postgres`
- `flask_app`
- `nextjs_app`

Start:

```bash
docker compose up -d
```

Stop:

```bash
docker compose down
```

Logs:

```bash
docker compose logs -f
docker compose logs -f postgres
docker compose logs -f flask_app
docker compose logs -f nextjs_app
```

With default values, the services are expected at:

- Frontend: <http://localhost:3000>
- API: <http://localhost:5050>
- PostgreSQL: `localhost:5432`

## Manual Frontend Setup

From `client/`:

```bash
pnpm install
pnpm dev
```

Available scripts:

```bash
pnpm dev
pnpm build
pnpm start
pnpm lint
```

The frontend requires `NEXT_PUBLIC_API_URL`.

## Manual Backend Setup

From `server/`:

```bash
python3 -m venv venv
source venv/bin/activate
pip install -r requirements.txt
flask run --host=0.0.0.0 --port=5050 --reload
```

The backend requires the `DB_*` environment variables to point to a reachable PostgreSQL database.

## Database Population

Automatic population:

- Set `POPULATE_DB=true`.
- Start the Flask container through Compose.
- `server/docker/entrypoint.py` waits for PostgreSQL and runs the population flow when the database is not already populated.

Manual population:

```bash
cd server
./scripts/populate_db.sh
```

Manual downgrade/cleanup:

```bash
cd server
./scripts/downgrade_db.sh
```

Important files:

- `server/sql/upgrade_schema.sql`
- `server/sql/functions/`
- `server/sql/indexes.sql`
- `server/sql/views.sql`
- `server/data_generators/populate.py`
- `server/data_generators/downgrade.py`

## Seeded Accounts

Seeded test credentials are documented in `LOGINS.md`. They are for local coursework demonstrations only.

## Setup Status

This project is archived. Setup commands and dependencies should be revalidated before using the repository as the base for new work.
