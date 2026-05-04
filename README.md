# CEFER Management

> Status: archived academic coursework reference.

CEFER Management is a full-stack academic system for managing sports-facility reservations, activities, equipment, users, external invitations, and reporting workflows for the CEFER context at USP. It was built for the SCC0641 database laboratory coursework and is preserved as a database-first portfolio and study project, not as an actively maintained production product.

The project combines a PostgreSQL relational model, a Flask API, a Next.js client, SQL functions/views/triggers, synthetic data generators, and Docker Compose orchestration.

## What This Project Solves

The system models a sports center where different user profiles need different workflows:

- Internal USP users can view available activities, enroll in activities, reserve installations, reserve equipment, and invite external guests.
- Staff users can manage activities, participants, equipment reservations, and installation reservations.
- Administrators can manage users, installations, equipment, events, extension groups, activities, reservations, and registration requests.
- External users can authenticate through invitation tokens and accept or reject participation invitations.
- Reports and database views expose analytical summaries for reservations, activities, equipment, installations, and participation.

## Main Features

- Role-based web UI for administrators, staff, internal users, and external invitees.
- Flask API grouped by domain under `server/app/routes/`.
- PostgreSQL schema, indexes, views, PL/pgSQL functions, and triggers under `server/sql/`.
- Synthetic data generation under `server/data_generators/`.
- Session-based authentication for internal users.
- Token-based external invite flow.
- Docker Compose setup for PostgreSQL, Flask, and Next.js.
- Academic report artifacts preserved under `relatorio.md` and `docs/entrega_final/`.

## Technology Stack

| Area | Technology |
| --- | --- |
| Frontend | Next.js 16, React 19, TypeScript, Tailwind CSS |
| UI/data | Radix UI primitives, TanStack Query, TanStack Table, Zustand, Recharts, Lucide React |
| Backend | Python 3.12, Flask, Flask-Cors, psycopg2 |
| Database | PostgreSQL 17, SQL scripts, PL/pgSQL functions, triggers, views, indexes |
| Local orchestration | Docker Compose |
| Data generation | Python scripts with Faker |

## Project Structure

```text
cefer/
|-- client/                  # Next.js App Router frontend
|   |-- app/                 # Role-based routes and pages
|   |-- components/          # UI primitives and domain components
|   |-- hooks/               # TanStack Query hooks and API wrappers
|   `-- lib/                 # API client, auth state, query provider, utilities
|-- server/                  # Flask and PostgreSQL backend
|   |-- app/                 # App factory, routes, services, database session
|   |-- data_generators/     # Synthetic data population modules
|   |-- docker/              # Flask Dockerfile and entrypoint
|   |-- scripts/             # Database population and downgrade helpers
|   `-- sql/                 # Schema, SQL queries, views, indexes, functions
|-- docs/                    # Operational documentation and course artifacts
|-- LOGINS.md                # Seeded development credentials
|-- relatorio.md             # Historical academic report artifact
|-- docker-compose.yml       # Local full-stack orchestration
`-- .env.example             # Environment variable template
```

## Documentation Map

- [Documentation index](docs/index.md)
- [Project overview](docs/overview.md)
- [Architecture](docs/architecture.md)
- [Setup](docs/setup.md)
- [Development guide](docs/development.md)
- [API reference](docs/api.md)
- [Database guide](docs/database.md)
- [Deployment notes](docs/deployment.md)
- [Security notes](docs/security.md)
- [Troubleshooting](docs/troubleshooting.md)
- [Client README](client/README.md)
- [Server README](server/README.md)
- [Seeded logins](LOGINS.md)

## Prerequisites

For the Docker Compose workflow:

- Docker
- Docker Compose

For manual local development:

- Node.js 20+
- pnpm
- Python 3.12+
- PostgreSQL 17 or access to the Compose `postgres` service

## Environment Configuration

Copy `.env.example` to `.env` and adjust values as needed:

```bash
cp .env.example .env
```

Important variables:

| Variable | Purpose |
| --- | --- |
| `POSTGRES_USER`, `POSTGRES_PASSWORD`, `POSTGRES_DB`, `POSTGRES_PORT` | PostgreSQL container settings |
| `DB_HOST`, `DB_PORT`, `DB_NAME`, `DB_USER`, `DB_PASSWORD`, `DB_SCHEMA` | Flask database connection settings |
| `FLASK_SECRET_KEY` | Flask session signing secret |
| `FLASK_DEBUG`, `FLASK_RUN_HOST`, `FLASK_RUN_PORT`, `FLASK_PORT` | Flask runtime and exposed port settings |
| `NEXT_PUBLIC_API_URL` | Browser-visible base URL for the Flask API |
| `NEXTJS_PORT`, `NODE_ENV` | Next.js runtime settings |
| `CORS_ORIGINS` | Comma-separated allowed browser origins |
| `POPULATE_DB` | Enables automatic database population in the Flask container |

Use a strong `FLASK_SECRET_KEY` outside local coursework testing. Do not publish real database credentials.

## Running With Docker Compose

The repository defines three services in `docker-compose.yml`:

- `postgres`: PostgreSQL 17 with persisted data under `server/pgdata`.
- `flask_app`: Flask API built from `server/docker/Dockerfile`.
- `nextjs_app`: Next.js development server built from `client/Dockerfile.dev`.

Start the stack:

```bash
docker compose up -d
```

Expected local URLs with default `.env.example` values:

- Frontend: <http://localhost:3000>
- Flask API: <http://localhost:5050>
- PostgreSQL: `localhost:5432`

Stop the stack:

```bash
docker compose down
```

Inspect logs:

```bash
docker compose logs -f
docker compose logs -f flask_app
docker compose logs -f nextjs_app
docker compose logs -f postgres
```

## Manual Development Notes

Frontend:

```bash
cd client
pnpm install
pnpm dev
```

Backend:

```bash
cd server
python3 -m venv venv
source venv/bin/activate
pip install -r requirements.txt
flask run --host=0.0.0.0 --port=5050 --reload
```

The Flask backend expects database variables from `.env`. For browser access, `NEXT_PUBLIC_API_URL` must point to the Flask API and `CORS_ORIGINS` must include the frontend origin.

## Database Population

The database is database-first. Schema and application data are managed by SQL and Python generator assets:

- `server/sql/upgrade_schema.sql`: main relational schema.
- `server/sql/functions/`: PL/pgSQL auth, admin, staff, internal, and trigger functions.
- `server/sql/views.sql`: reporting and table-view surfaces.
- `server/sql/indexes.sql`: explicit indexes.
- `server/data_generators/populate.py`: coordinated synthetic data population.
- `server/data_generators/downgrade.py`: downgrade/cleanup path.

When `POPULATE_DB=true`, the Flask container entrypoint checks whether the database is populated and runs the generator flow if needed.

Manual helper scripts:

```bash
cd server
./scripts/populate_db.sh
./scripts/downgrade_db.sh
```

Seeded accounts are documented in [LOGINS.md](LOGINS.md). They are development/coursework credentials only.

## Available Scripts

Frontend scripts in `client/package.json`:

| Script | Purpose |
| --- | --- |
| `pnpm dev` | Starts the Next.js development server |
| `pnpm build` | Builds the Next.js application |
| `pnpm start` | Starts the production Next.js server |
| `pnpm lint` | Runs ESLint |

Backend helper scripts:

| Script | Purpose |
| --- | --- |
| `server/scripts/populate_db.sh` | Applies schema assets and synthetic data |
| `server/scripts/downgrade_db.sh` | Runs downgrade cleanup |

## API Surface

The Flask API is grouped by blueprint:

- `/auth`: login, registration, session inspection, password updates, registration approvals, external token login.
- `/admin`: administrative dashboards and CRUD for users, installations, equipment, events, activities, and reservations.
- `/internal`: internal-user dashboard, activity enrollment, reservations, equipment, and invitations.
- `/staff`: staff dashboard, activity participant management, reservations, equipment and installation reservation management.
- `/external`: external invite dashboard and invite accept/reject actions.
- `/extension_group`: extension group management.
- `/reports`: analytical report data.
- `/views`: API access to SQL views.
- `/debug`: database status, population, and cleanup helpers.

See [docs/api.md](docs/api.md) for the route map.

## Tests

No active automated test setup was identified in the current codebase. There are no test scripts in `client/package.json`, no Python test framework configuration, and no test files found during this documentation pass. The client lockfile references `@playwright/test` through dependencies, but no Playwright project setup or script was identified.

## Deployment Status

The repository contains Dockerfiles and Docker Compose configuration, but no CI/CD workflow, cloud hosting configuration, production release process, rollback process, or managed deployment target was identified.

Treat the deployment assets as local/containerization support for the archived coursework unless they are revalidated and hardened.

## Security Notes

- Flask sessions depend on `FLASK_SECRET_KEY`.
- API requests use cookies with `credentials: "include"` on the frontend.
- Role checks are enforced with session-backed decorators in `server/app/services/auth/decorators.py`.
- External users authenticate through invitation tokens stored in Flask session state.
- CORS is controlled by `CORS_ORIGINS`.
- Debug database routes exist under `/debug` and should not be exposed in a real production environment.
- Seeded credentials in `LOGINS.md` are intentionally weak and must remain local-only.

See [docs/security.md](docs/security.md) for details.

## Historical Academic Artifacts

The repository also contains course deliverables:

- `relatorio.md`: Portuguese academic technical report.
- `docs/entrega_final/`: final LaTeX/PDF deliverables.
- `docs/entregas_parciais/`: partial course deliverables.
- `docs/*.pdf`: original assignment or delivery PDFs.

These files are preserved as historical artifacts. They are not treated as living operational documentation in this refresh.

## Current Status

This project is archived. The codebase remains useful for studying a full-stack, database-first academic application, but dependencies, Docker assumptions, credentials, debug routes, and deployment behavior should be reviewed before any reuse.

## License

TODO: not identified in the current codebase.
