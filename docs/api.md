# API Reference

The backend API is implemented with Flask blueprints under `server/app/routes/`. The route modules are the source of truth for exact request bodies, response fields, and validation behavior.

The frontend calls the API through helpers in `client/lib/api.ts`, using `NEXT_PUBLIC_API_URL` and cookie credentials.

## Response Pattern

Most endpoints return JSON objects with a `success` boolean and either domain data or a `message` field. Error responses commonly use:

```json
{
  "success": false,
  "message": "Error message"
}
```

Many messages in the current application code are still in Portuguese because this documentation refresh does not change runtime strings.

## Authentication Model

- Internal login uses `/auth/login` and stores `user_id`, user metadata, and `profile_access` in Flask session state.
- External login uses `/auth/login/external` and stores invitation token data in Flask session state.
- Frontend requests include cookies with `credentials: "include"`.
- Role-protected endpoints use decorators from `server/app/services/auth/decorators.py`.

## `/auth`

Defined in `server/app/routes/auth.py`.

| Method | Path | Purpose | Access |
| --- | --- | --- | --- |
| GET | `/auth/login` | Returns redirect metadata when already logged in | Public/session-aware |
| POST | `/auth/login` | Authenticates an internal user by email/CPF and password | Public |
| GET | `/auth/register` | Returns redirect metadata when already logged in | Public/session-aware |
| POST | `/auth/register` | Creates a registration request | Public |
| GET | `/auth/pending-registrations` | Lists pending registration requests | Admin |
| GET | `/auth/approved-registrations` | Lists approved registration requests | Admin |
| GET | `/auth/rejected-registrations` | Lists rejected registration requests | Admin |
| POST | `/auth/approve-registration` | Approves a registration request | Admin |
| POST | `/auth/reject-registration` | Rejects a registration request | Admin |
| GET | `/auth/me` | Returns the current session user | Authenticated or external session |
| POST | `/auth/login/external` | Authenticates an external invitee by token | Public |
| POST | `/auth/update-password` | Updates the current internal user's password | Authenticated internal user |
| GET | `/auth/logout` | Clears the Flask session | Session-aware |

## `/admin`

Defined in `server/app/routes/admin/`. All listed routes require the `admin` role.

| Method | Path | Purpose |
| --- | --- | --- |
| GET | `/admin/` | Admin dashboard data |
| GET | `/admin/users` | List users |
| GET | `/admin/users/<cpf>` | Get one user |
| POST | `/admin/users` | Create a user/person/internal/staff/educator record |
| PUT | `/admin/users/<cpf>` | Update user and permission-related data |
| DELETE | `/admin/users/<cpf>` | Delete a user |
| GET | `/admin/installations` | List installations |
| GET | `/admin/installations/<installation_id>` | Get one installation |
| POST | `/admin/installations` | Create an installation |
| PUT | `/admin/installations/<installation_id>` | Update an installation |
| DELETE | `/admin/installations/<installation_id>` | Delete an installation |
| GET | `/admin/equipment` | List equipment |
| GET | `/admin/equipment/<equipment_id>` | Get one equipment item |
| POST | `/admin/equipment` | Create equipment |
| PUT | `/admin/equipment/<equipment_id>` | Update equipment |
| DELETE | `/admin/equipment/<equipment_id>` | Delete equipment |
| GET | `/admin/events` | List events |
| GET | `/admin/events/<event_id>` | Get one event |
| POST | `/admin/events` | Create an event |
| PUT | `/admin/events/<event_id>` | Update an event |
| DELETE | `/admin/events/<event_id>` | Delete an event |
| GET | `/admin/activities` | List activities |
| GET | `/admin/activities/<activity_id>` | Get one activity |
| POST | `/admin/activities` | Create an activity |
| PUT | `/admin/activities/<activity_id>` | Update an activity |
| DELETE | `/admin/activities/<activity_id>` | Delete an activity |
| GET | `/admin/reservations` | List reservations |

## `/internal`

Defined in `server/app/routes/internal/dashboard.py`. These routes require one of `internal`, `staff`, or `admin`.

| Method | Path | Purpose |
| --- | --- | --- |
| GET | `/internal/` | Internal dashboard data and optional availability filters |
| GET | `/internal/activities` | List available activities with optional filters |
| POST | `/internal/activities/enroll` | Enroll the current user in an activity |
| POST | `/internal/reservations/installation` | Reserve an installation |
| POST | `/internal/reservations/equipment` | Reserve equipment |
| GET | `/internal/equipment` | List available reservable equipment |
| GET | `/internal/invites` | List invitations for the current internal user |
| POST | `/internal/invites` | Create an external invitation |
| DELETE | `/internal/reservations/installation/<reservation_id>` | Cancel an installation reservation |
| DELETE | `/internal/reservations/equipment/<reservation_id>` | Cancel an equipment reservation |
| DELETE | `/internal/invites/<invite_id>` | Delete an external invitation |

## `/staff`

Defined in `server/app/routes/staff/`. These routes require `staff` or `admin`.

| Method | Path | Purpose |
| --- | --- | --- |
| GET | `/staff/` | Staff dashboard data |
| GET | `/staff/activities/<activity_id>/participants` | List participants for an activity |
| POST | `/staff/activities/<activity_id>/participants` | Add a participant to an activity |
| DELETE | `/staff/activities/<activity_id>/participants/<cpf>` | Remove a participant from an activity |
| GET | `/staff/activities/<activity_id>` | Get activity detail |
| POST | `/staff/activities` | Create an activity |
| PUT | `/staff/activities/<activity_id>` | Update an activity |
| DELETE | `/staff/activities/<activity_id>` | Delete an activity |
| GET | `/staff/installations/reservations` | List installation reservations |
| DELETE | `/staff/installations/reservations/<reservation_id>` | Cancel an installation reservation |
| GET | `/staff/equipment/reservations` | List equipment reservations |
| DELETE | `/staff/equipment/reservations/<reservation_id>` | Cancel an equipment reservation |

## `/external`

Defined in `server/app/routes/external/dashboard.py`. These routes require an external invitation session.

| Method | Path | Purpose |
| --- | --- | --- |
| GET | `/external/dashboard` | Get external invite dashboard data |
| POST | `/external/accept` | Accept an external invite |
| POST | `/external/reject` | Reject an external invite |

## `/extension_group`

Defined in `server/app/routes/extension_group/`. These routes require `admin`.

| Method | Path | Purpose |
| --- | --- | --- |
| GET | `/extension_group/` | List extension groups |
| POST | `/extension_group/create` | Create an extension group |
| POST | `/extension_group/update` | Update an extension group |
| DELETE | `/extension_group/delete` | Delete an extension group |

## `/reports`

Defined in `server/app/routes/reports/overview.py`.

| Method | Path | Purpose | Access |
| --- | --- | --- | --- |
| GET | `/reports/overview` | Returns analytical report datasets | Authenticated user |

The report endpoint reads SQL files from `server/sql/queries/reports/`.

## `/views`

Defined in `server/app/routes/views/api.py`. These routes require authentication.

| Method | Path | Backing SQL view/query |
| --- | --- | --- |
| GET | `/views/reservas-completas` | `queries/views/reservas_completas.sql` |
| GET | `/views/atividades-completas` | `queries/views/atividades_completas.sql` |
| GET | `/views/equipamentos-disponiveis` | `queries/views/equipamentos_disponiveis.sql` |
| GET | `/views/instalacoes-ocupacao` | `queries/views/instalacoes_ocupacao.sql` |
| GET | `/views/reservas-equipamentos-completas` | `queries/views/reservas_equipamentos_completas.sql` |

## `/debug`

Defined in `server/app/routes/debug/database.py`.

| Method | Path | Purpose |
| --- | --- | --- |
| GET | `/debug/check-db-status` | Inspect database status |
| POST | `/debug/populate-db` | Populate the database |
| POST | `/debug/clear-db` | Clear/downgrade the database |

These routes are development helpers and should not be exposed in a real production environment.
