# db_project Client

> **Status:** Archived
> Next.js frontend for the SCC0641 database coursework project.

## Summary

- Archived Next.js frontend for the sports-facility reservation and activity-management coursework system.
- Solves the browser UI for role-based users, authentication, reports, resources, activities, and reservation workflows.
- Main stack: Next.js 16, React 19, TypeScript, Tailwind CSS, Radix UI primitives, TanStack Query/Table, Zustand, and Recharts.
- Current status: archived and documented as part of the root `db_project` coursework reference.
- Technical value: shows role-separated App Router screens and shared protected-route/layout primitives.

Frontend app for the sports-facility reservation and activity-management system. It provides role-based screens for administrators, internal users, staff, external users, authentication, reports, and resource/reservation management.

## Features

- Login, registration, password-change, logout, and pending-registration pages.
- Admin dashboards and management pages.
- Internal user dashboards for activities, installations, invites, and reservations.
- Staff dashboards for participants, resources, activities, and active reservations.
- External dashboard.
- Report overview with chart components.
- Shared layout, navbar, protected route, message, and UI primitives.

## Tech Stack

- Next.js 16
- React 19
- TypeScript
- Tailwind CSS
- Radix UI primitives
- TanStack React Query
- TanStack Table
- Zustand
- Recharts

## Getting Started

The root `db_project/README.md` documents the Docker Compose and local-development flow. This package's scripts are declared in `package.json`.

## Usage

Primary scripts:

- `pnpm dev`
- `pnpm build`
- `pnpm start`
- `pnpm lint`

## Project Structure

```text
client/
├── app/
│   ├── admin/
│   ├── auth/
│   ├── external/
│   ├── internal/
│   ├── reports/
│   └── staff/
├── components/
├── Dockerfile
└── package.json
```

## Architecture

The App Router owns page routing by user role. Shared components provide shell, auth initialization, protected routes, data tables, report charts, and form surfaces. The Dockerfile builds a standalone Next.js production image.
