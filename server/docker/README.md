# db_project Server Docker

> **Status:** Archived
> Docker runtime wrapper for the Flask backend.

## Summary

- Docker support folder for the archived `db_project/server` Flask backend.
- Solves container startup by installing Python dependencies, setting Flask runtime defaults, and delegating boot logic to `docker/entrypoint.py`.
- Main stack: Docker, Python 3.12 slim, Flask, and `requirements.txt`.
- Current status: archived and used only as part of the root Docker Compose workflow.
- Technical value: isolates backend container details from the broader coursework README.

Docker support folder for the `db_project/server` Flask app.

## Features

- Python 3.12 slim image.
- `python3-tk` system dependency installation.
- `requirements.txt` install step.
- Flask environment variables.
- Entrypoint through `docker/entrypoint.py`.

## Tech Stack

- Docker
- Python 3.12
- Flask

## Usage

This folder is used by the root `docker-compose.yml` and backend Docker workflow. Prefer the root README for orchestration details.

## Project Structure

```text
server/docker/
├── Dockerfile
└── entrypoint.py
```
