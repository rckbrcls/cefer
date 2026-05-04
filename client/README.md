# CEFER Management Client

> Status: archived academic coursework frontend.

This package contains the Next.js frontend for CEFER Management. It provides the browser interface for authentication, role-based dashboards, resource management, reservations, external invitations, and reporting.

## Responsibilities

- Render role-specific application areas for admin, staff, internal, and external users.
- Call the Flask API through `NEXT_PUBLIC_API_URL`.
- Maintain current-user state and client-side role redirects.
- Provide data tables, forms, charts, and management screens for the PostgreSQL-backed domain.

## Stack

- Next.js 16 with App Router.
- React 19.
- TypeScript.
- Tailwind CSS.
- Radix UI primitives.
- TanStack Query.
- TanStack Table.
- Zustand.
- Recharts.
- Lucide React.

## Structure

```text
client/
|-- app/
|   |-- admin/       # Admin dashboard and management routes
|   |-- auth/        # Login, register, logout, password, registration review
|   |-- external/    # External invitation dashboard
|   |-- internal/    # Internal user activities, reservations, invites
|   |-- reports/     # Report overview
|   `-- staff/       # Staff activities, participants, resources, reservations
|-- components/
|   |-- admin/       # Admin management components
|   |-- internal/    # Internal user workflow components
|   |-- reports/     # Chart components
|   |-- staff/       # Staff management components
|   |-- ui/          # Shared UI primitives
|   `-- views/       # SQL-view table components
|-- hooks/           # Domain data hooks
|-- lib/             # API, auth, query, and utility code
|-- public/          # Static assets
|-- Dockerfile       # Production-style standalone image
|-- Dockerfile.dev   # Compose development image
`-- package.json
```

## Routing Areas

| Area | Purpose |
| --- | --- |
| `app/auth/` | Internal login, external login, registration, pending registration review, password change, logout |
| `app/admin/` | Administrator dashboard and management screens |
| `app/internal/` | Internal user dashboard, activities, installations, invites, reservations |
| `app/staff/` | Staff dashboard, activities, participants, active reservations, resources |
| `app/external/` | External invite dashboard |
| `app/reports/` | Analytical report overview |

## Data Access

The shared API helpers live in `client/lib/api.ts`:

- `apiGet`
- `apiPost`
- `apiPut`
- `apiDelete`

They resolve the backend URL through `client/lib/utils.ts` and send cookies with `credentials: "include"`.

Domain hooks in `client/hooks/` wrap those helpers for users, auth, reservations, activities, equipment, events, dashboards, invites, installations, extension groups, and participants.

## Authentication And Route Protection

Important files:

- `client/lib/auth.ts`: fetches `/auth/me` and exposes role helpers.
- `client/lib/authStore.ts`: stores current user state with Zustand.
- `client/components/AuthInitializer.tsx`: initializes auth state.
- `client/components/ProtectedRoute.tsx`: redirects unauthenticated or unauthorized users.

Frontend route protection improves navigation, but backend Flask decorators remain the enforcement layer.

## Environment

The client requires:

```text
NEXT_PUBLIC_API_URL=http://localhost:5050
```

When running in a browser, this value must point to the Flask API URL reachable from the browser.

## Scripts

```bash
pnpm dev
pnpm build
pnpm start
pnpm lint
```

No active test script was identified in `package.json`.

## Docker

- `Dockerfile.dev` is used by the root `docker-compose.yml` for the development container.
- `Dockerfile` builds a production-style standalone Next.js image using `pnpm build`.
- `next.config.ts` enables standalone output only when `NODE_ENV=production`.

## Notes

- Runtime UI strings were not changed by this documentation refresh.
- The root `README.md` and `docs/setup.md` describe the full-stack setup.
- This frontend is archived coursework and should be revalidated before reuse.
