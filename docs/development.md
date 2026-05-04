# Development Guide

This guide explains the current repository organization and development patterns. It documents the codebase as it exists; it does not introduce new conventions or runtime behavior.

## Repository Organization

```text
cefer/
|-- client/              # Next.js frontend
|-- server/              # Flask backend and SQL assets
|-- docs/                # Living docs plus historical course artifacts
|-- LOGINS.md            # Seeded local credentials
|-- docker-compose.yml   # Local orchestration
`-- .env.example         # Environment template
```

## Frontend Patterns

The frontend is organized around Next.js App Router route groups:

- `client/app/auth/`
- `client/app/admin/`
- `client/app/internal/`
- `client/app/staff/`
- `client/app/external/`
- `client/app/reports/`

Shared UI and data code lives in:

- `client/components/`: layout, navbar, protected route, domain managers, tables, charts, and UI primitives.
- `client/hooks/`: domain hooks for auth, users, reservations, activities, equipment, events, dashboards, invites, installations, extension groups, and participants.
- `client/lib/api.ts`: shared API helpers for GET, POST, PUT, and DELETE.
- `client/lib/auth.ts`: current user and role helpers.
- `client/lib/authStore.ts`: Zustand auth state.
- `client/lib/queryClient.tsx`: TanStack Query provider.

When adding a frontend feature, follow the existing pattern:

1. Add or update a route under `client/app/<area>/`.
2. Put reusable UI in `client/components/<area>/` when it belongs to a role/domain.
3. Put API state in `client/hooks/` when multiple components can reuse it.
4. Use `apiGet`, `apiPost`, `apiPut`, or `apiDelete` from `client/lib/api.ts`.
5. Use `ProtectedRoute` when the page requires role-based access.

## Backend Patterns

The Flask backend uses an app factory:

- `server/app/__init__.py`: creates the Flask app.
- `server/app/config.py`: reads environment-backed configuration.
- `server/app/extensions.py`: registers CORS and request-scoped database sessions.
- `server/app/routes/__init__.py`: initializes and registers blueprints.

Route modules are grouped by domain:

- `server/app/routes/auth.py`
- `server/app/routes/admin/`
- `server/app/routes/internal/`
- `server/app/routes/staff/`
- `server/app/routes/external/`
- `server/app/routes/extension_group/`
- `server/app/routes/reports/`
- `server/app/routes/views/`
- `server/app/routes/debug/`

When adding backend behavior, follow the existing pattern:

1. Add route logic to the relevant blueprint module.
2. Use decorators from `server/app/services/auth/decorators.py` for authentication and roles.
3. Put SQL in `server/sql/queries/<domain>/` rather than embedding large SQL strings in Python.
4. Execute SQL through `server/app/services/database/executor.py` when possible.
5. Return JSON with a `success` field and a domain-specific payload or `message`.

## SQL Asset Patterns

The project intentionally keeps database logic visible:

- Schema: `server/sql/upgrade_schema.sql`
- Views: `server/sql/views.sql`
- Indexes: `server/sql/indexes.sql`
- Functions and triggers: `server/sql/functions/`
- Route queries: `server/sql/queries/`
- Downgrades: `server/sql/downgrades/` and `server/sql/downgrade_schema.sql`

Query files are grouped by route/domain, such as `admin`, `auth`, `internal`, `staff`, `reports`, `views`, and `external`.

## Data Generation

Synthetic data generation lives under `server/data_generators/` and is coordinated by `server/data_generators/populate.py`.

Generator modules are grouped by domain:

- `pessoas/`
- `infraestrutura/`
- `reservas/`
- `atividades/`
- `eventos/`
- `grupos/`
- `auth/`

The generator flow is used by both manual scripts and the Docker entrypoint.

## Scripts And Checks

Frontend scripts:

```bash
cd client
pnpm dev
pnpm build
pnpm start
pnpm lint
```

Backend helper scripts:

```bash
cd server
./scripts/populate_db.sh
./scripts/downgrade_db.sh
```

No active automated test setup was identified in the current codebase. There are no package scripts for tests, no Python test framework configuration, and no test files identified during this documentation refresh.

## Development Cautions

- Do not expose seeded credentials from `LOGINS.md` outside local demonstrations.
- Do not expose `/debug` database routes in a production environment.
- Keep `NEXT_PUBLIC_API_URL` and `CORS_ORIGINS` aligned.
- Review any change to `server/sql/upgrade_schema.sql` together with queries, views, generators, and downgrade scripts.
- Treat `relatorio.md`, `docs/entrega_final/`, and `docs/entregas_parciais/` as historical course artifacts unless intentionally updating coursework deliverables.
