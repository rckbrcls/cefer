# Security Notes

This project is archived coursework and should not be treated as production-ready without a security review. The notes below document the security-relevant behavior that exists in the current codebase.

## Authentication

Internal users authenticate through `/auth/login` in `server/app/routes/auth.py`.

The login flow:

- Accepts email/CPF and password.
- Calls `server/sql/queries/auth/login_user.sql`.
- Stores user identity in Flask session keys such as `user_id`, `user_email`, `user_nome`, and `profile_access`.

Password hashing and verification are implemented in SQL functions under `server/sql/functions/auth_functions.sql`.

External users authenticate through `/auth/login/external` with an invitation token. Successful external login stores token and invite metadata in Flask session state.

## Authorization

Backend authorization uses decorators in `server/app/services/auth/decorators.py`:

- `require_auth`
- `require_role`
- `require_external_auth`

Role access is based on session state for route checks. The frontend also uses `client/components/ProtectedRoute.tsx` and role helpers in `client/lib/auth.ts` for client-side redirects, but backend decorators are the meaningful enforcement point.

## Session Secret

Flask sessions depend on `FLASK_SECRET_KEY`.

The template uses a placeholder value. Any non-local deployment must provide a strong secret and avoid committing real secrets.

## CORS And Cookies

The frontend sends API requests with `credentials: "include"` in `client/lib/api.ts`.

The backend enables Flask-Cors with `supports_credentials=True` and allowed origins from `CORS_ORIGINS` in `server/app/extensions.py`.

Keep `NEXT_PUBLIC_API_URL` and `CORS_ORIGINS` aligned. Inconsistent hostnames such as `localhost` versus `127.0.0.1` can affect browser cookie behavior.

## Seeded Credentials

`LOGINS.md` documents seeded test accounts and a test external invite token. These credentials are intentionally simple and are for local coursework demonstrations only.

Do not expose a seeded database to public networks.

## Debug Routes

Debug database routes exist under `/debug`:

- `/debug/check-db-status`
- `/debug/populate-db`
- `/debug/clear-db`

These routes are useful for local development but should be removed, disabled, or strictly protected before any production-like deployment.

## External Invitation Tokens

External invite access depends on tokens stored in `CONVITE_EXTERNO`. The token login flow is implemented in `/auth/login/external`, with external dashboard and accept/reject behavior under `/external`.

If reused, token lifetime, entropy, logging, and invalidation should be reviewed.

## Sensitive Data

The database stores personal identifiers and contact data such as CPF, NUSP, email, phone, birth date, invitee documents, and login audit data.

Any real deployment would need:

- A privacy review.
- Access controls for administrative endpoints.
- Database backup protection.
- Secret management.
- Transport security through HTTPS.
- A policy for seeded/test data removal.

## Known Risks

- The project is archived and not hardened for production.
- Debug routes can mutate database state.
- Seeded credentials are public in repository documentation.
- Some authentication paths include verbose debug logging.
- No automated security tests or CI checks were identified.
- No production-grade deployment, TLS, monitoring, or secret management configuration was identified.
