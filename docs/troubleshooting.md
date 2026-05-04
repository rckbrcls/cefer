# Troubleshooting

This guide covers issues that are likely in the current Docker Compose and local development setup.

## Frontend Cannot Reach The Flask API

Symptoms:

- Browser requests fail with network errors.
- The frontend reports that it cannot connect to the server.
- Login, registration, or dashboard data does not load.

Checks:

- Confirm `NEXT_PUBLIC_API_URL` points to the Flask API, for example `http://localhost:5050`.
- Confirm the Flask service exposes the same host port through `FLASK_PORT`.
- Confirm `CORS_ORIGINS` includes the frontend origin, for example `http://localhost:3000`.
- Confirm browser requests include cookies. The frontend API helpers use `credentials: "include"`.

Relevant files:

- `.env.example`
- `client/lib/utils.ts`
- `client/lib/api.ts`
- `server/app/extensions.py`

## Database Does Not Initialize

Symptoms:

- API responses fail with database errors.
- Tables are missing.
- The Flask logs mention schema or population failures.

Checks:

- Confirm PostgreSQL environment variables in `.env`.
- Confirm `DB_HOST=postgres` when using Docker Compose.
- Confirm `DB_HOST=localhost` only when the backend is running outside Compose against a locally reachable database.
- Confirm `POPULATE_DB=true` if automatic sample data population is expected.
- Inspect schema assets under `server/sql/`.
- Inspect generator assets under `server/data_generators/`.

Relevant files:

- `server/app/services/database/bootstrap.py`
- `server/data_generators/populate.py`
- `server/sql/upgrade_schema.sql`
- `server/sql/functions/`
- `server/sql/views.sql`

## Missing `.env`

Symptoms:

- Compose services start with missing or empty variable values.
- The client cannot determine `NEXT_PUBLIC_API_URL`.
- Flask uses fallback defaults such as `change-me` or `password`.

Fix:

```bash
cp .env.example .env
```

Then review every value before starting services.

## Port Conflicts

Symptoms:

- Docker Compose fails to bind ports.
- The frontend or backend starts on a different port than expected.

Checks:

- `POSTGRES_PORT` controls the host PostgreSQL port.
- `FLASK_PORT` controls the host Flask API port.
- `NEXTJS_PORT` controls the host Next.js port.
- `FLASK_RUN_PORT` controls the Flask port inside the Flask container.

If host ports are busy, change the exposed host port values in `.env`.

## Database Population Fails

Symptoms:

- Seeded users from `LOGINS.md` cannot log in.
- Tables exist but contain little or no data.
- The Flask entrypoint logs generator errors.

Checks:

- Confirm PostgreSQL is reachable with the configured `DB_*` values.
- Confirm schema creation completed before generator execution.
- Review `server/data_generators/populate.py`.
- Review domain generator modules under `server/data_generators/`.
- Review `server/scripts/populate_db.sh` for the manual helper path.

## Downgrade Or Cleanup Fails

Symptoms:

- Old data remains after cleanup.
- Schema objects are only partially removed.

Checks:

- Review `server/data_generators/downgrade.py`.
- Review SQL cleanup files under `server/sql/downgrades/`.
- Review `server/sql/downgrade_schema.sql`.
- Confirm the cleanup process is running against the intended database.

## Authentication Works In API But Pages Redirect

Symptoms:

- `/auth/me` returns a user, but the frontend redirects unexpectedly.
- A page is blank after login.

Checks:

- Confirm the user roles returned by `/auth/me`.
- Confirm the page's `ProtectedRoute` allowed roles.
- Confirm browser cookies are sent to the Flask API.
- Confirm `CORS_ORIGINS` and `NEXT_PUBLIC_API_URL` refer to matching hostnames. Mixing `localhost` and `127.0.0.1` can affect cookies.

Relevant files:

- `client/components/ProtectedRoute.tsx`
- `client/lib/auth.ts`
- `client/lib/authStore.ts`
- `server/app/routes/auth.py`

## External Invitation Token Fails

Symptoms:

- External login rejects a token.
- The external dashboard cannot load after login.

Checks:

- Confirm the token exists in `CONVITE_EXTERNO`.
- Confirm seeded test token data from `LOGINS.md` exists after population.
- Confirm `/auth/login/external` succeeds before opening `/external/dashboard`.
- Review `server/sql/queries/auth/login_external_by_token.sql`.
- Review `server/app/routes/external/dashboard.py`.

## No Automated Test Command

No current automated test setup was identified. Use static review and manual local validation if this archived coursework project is revived.
