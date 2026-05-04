# Project Overview

CEFER Management is an archived academic full-stack application for a sports-facility management domain. It was built for the SCC0641 database laboratory coursework and demonstrates a database-first implementation using PostgreSQL, Flask, and Next.js.

## Product Goal

The project models the operational workflows of a sports center where internal users, staff, administrators, and external invitees interact with the same reservation and activity database.

The main goal is to show a functioning application around a relational schema, not to provide a production-ready CEFER platform.

## User Profiles

| Profile | Main use cases |
| --- | --- |
| Internal USP user | Reserve installations, reserve equipment, enroll in activities, invite external users |
| Staff user | Manage activity participants, activities, equipment reservations, and installation reservations |
| Administrator | Manage users, resources, activities, events, extension groups, reservations, and registration approvals |
| External invitee | Access the system with an invitation token and accept or reject participation |

## Main Workflows

- Authentication and registration approval for internal users.
- External invitation token login for non-USP invitees.
- Installation reservation by date and time window.
- Equipment reservation by date and time window.
- Activity listing, filtering, and enrollment.
- Staff management of activities and participants.
- Administrative management of users, events, installations, equipment, extension groups, and registrations.
- Reporting over SQL views and analytical queries.

## What The Project Does

- Provides a role-based web interface under `client/app/`.
- Exposes a Flask API under `server/app/routes/`.
- Stores domain state in PostgreSQL tables defined by `server/sql/upgrade_schema.sql`.
- Applies database functions, triggers, indexes, and views from `server/sql/`.
- Populates realistic sample data using Python generator modules under `server/data_generators/`.
- Runs locally through `docker-compose.yml`.

## What The Project Does Not Do

- It does not provide a verified production deployment process.
- It does not include a current automated test suite.
- It does not include CI/CD configuration.
- It does not integrate with a real USP identity provider.
- It does not include a documented backup, monitoring, or rollback process.

## Current Status

The project is archived as coursework and portfolio material. The operational documentation explains what exists in the repository and how the pieces fit together, but any future reuse should start with dependency, security, and runtime validation.
