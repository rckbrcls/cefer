# Deployment Notes

This repository contains Docker assets, but no verified production deployment process was identified. Treat the deployment support as local/containerization infrastructure for archived coursework unless it is revalidated.

## Existing Container Assets

| File | Purpose |
| --- | --- |
| `docker-compose.yml` | Defines local `postgres`, `flask_app`, and `nextjs_app` services |
| `server/docker/Dockerfile` | Builds the Flask backend image from Python 3.12 slim |
| `server/docker/entrypoint.py` | Waits for PostgreSQL, optionally populates the database, then starts Flask |
| `client/Dockerfile.dev` | Builds the development Next.js container used by Compose |
| `client/Dockerfile` | Builds a production-style standalone Next.js image |
| `client/next.config.ts` | Enables `output: "standalone"` only when `NODE_ENV=production` |

## Docker Compose Services

`docker-compose.yml` defines:

- `postgres`: PostgreSQL 17, exposed through `${POSTGRES_PORT}`, with data mounted at `./server/pgdata`.
- `flask_app`: Flask API, built from `server/docker/Dockerfile`, exposed through `${FLASK_PORT}`.
- `nextjs_app`: Next.js development app, built from `client/Dockerfile.dev`, exposed through `${NEXTJS_PORT}`.

The Compose setup is suitable for local reproduction of the coursework stack. It is not a complete production deployment definition.

## Required Environment

Production-like use would need all variables from `.env.example`, especially:

- PostgreSQL credentials and database name.
- Flask database connection variables.
- `FLASK_SECRET_KEY`.
- `NEXT_PUBLIC_API_URL`.
- `CORS_ORIGINS`.
- Published service ports.

Use a strong secret key and non-default database credentials outside local testing.

## Not Identified

The current codebase does not include:

- GitHub Actions or other CI/CD workflow.
- Vercel, AWS, Supabase, Fly.io, Render, Railway, or other cloud deployment configuration.
- Production process manager configuration.
- Reverse proxy configuration.
- TLS/HTTPS setup.
- Backup or restore process.
- Rollback process.
- Monitoring or alerting configuration.

## Pre-Deployment Checklist

Before adapting this project for any real hosted environment:

- Remove or restrict `/debug` routes.
- Replace seeded credentials and weak defaults.
- Set a strong `FLASK_SECRET_KEY`.
- Review CORS origins.
- Confirm cookie/session behavior behind HTTPS.
- Review debug logging in authentication and invite flows.
- Add automated tests.
- Add database backup and migration strategy.
- Verify Docker images with current dependency versions.
