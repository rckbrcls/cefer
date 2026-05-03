# db_project Server

> **Status:** Archived
> Flask and PostgreSQL backend for the SCC0641 database coursework project.

## Summary

- Archived Flask/PostgreSQL backend for the sports-facility reservation and activity-management coursework system.
- Solves application routing, database access, SQL lifecycle scripts, data generation, downgrade helpers, and seed population for the assignment.
- Main stack: Python 3.12, Flask, Flask-Cors, psycopg2, Faker, PostgreSQL, SQL scripts, and Docker.
- Current status: archived and kept for coursework reference.
- Technical value: keeps schema, views, functions, indexes, triggers, downgrade behavior, and synthetic generators visible beside the Flask app.

Backend app for the sports-facility reservation and activity-management system. It contains the Flask app, SQL schema and views, database functions, downgrade scripts, and synthetic data generators used by the coursework environment.

## Features

- Flask app factory.
- Auth and home routes.
- SQL upgrade, downgrade, indexes, views, functions, and triggers.
- Data generators for people, infrastructure, reservations, activities, events, groups, auth, and audits.
- Populate and downgrade helper scripts.

## Tech Stack

- Python 3.12
- Flask
- Flask-Cors
- PostgreSQL
- psycopg2
- SQL scripts

## Getting Started

The root `db_project/README.md` documents the Docker Compose workflow and expected environment variables.

## Usage

Important local files:

- `wsgi.py` is the Flask entrypoint.
- `app/__init__.py` creates and configures the Flask app.
- `data_generators/populate.py` coordinates sample-data population.
- `sql/upgrade_schema.sql` and `sql/views.sql` define the database surface.

## Project Structure

```text
server/
├── app/
├── data_generators/
├── docker/
├── scripts/
├── sql/
├── requirements.txt
└── wsgi.py
```

## Architecture

The backend is database-first: SQL files define schema, views, functions, indexes, and downgrade behavior, while Flask exposes application routes and helper services. Data generators seed the relational model for testing and coursework demonstrations.
