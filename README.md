# flask-on-docker

[![CI](https://github.com/rsycz/flask-on-docker/actions/workflows/main.yml/badge.svg)](https://github.com/rsycz/flask-on-docker/actions/workflows/main.yml)

## Overview

This repository contains a containerized web application built with Flask, PostgreSQL, Gunicorn, and Nginx — the core of an Instagram-style tech stack. The app is fully orchestrated with Docker Compose and supports two deployment modes: a lightweight development environment with Flask's built-in server, and a production-ready environment where Gunicorn serves the Flask app behind an Nginx reverse proxy. Users can upload image files through the web interface and retrieve them via URL, demonstrating a complete request lifecycle across all services.

<!-- Animated GIF placeholder -->
<!-- ![Demo](demo.gif) -->

---

## Build Instructions

### Prerequisites

- [Docker](https://docs.docker.com/get-docker/) and [Docker Compose](https://docs.docker.com/compose/install/) must be installed on your machine.

### Development

The development environment uses Flask's built-in server and mounts your local source code into the container so changes are reflected immediately without rebuilding.

```bash
# Build and start the development containers
docker compose up -d --build

# Create the database tables
docker compose exec web python manage.py create_db
```

The app will be available at **http://localhost:5000**.

To bring the services down:
```bash
docker compose down -v
```

### Production

The production environment uses Gunicorn as the WSGI server and Nginx as a reverse proxy. Static and media files are served directly by Nginx.

```bash
# Build and start the production containers
docker compose -f docker-compose.prod.yml up -d --build

# Create the database tables
docker compose -f docker-compose.prod.yml exec web python manage.py create_db
```

The app will be available at **http://localhost:1337**.

To bring the services down:
```bash
docker compose -f docker-compose.prod.yml down -v
```

### Uploading an Image

Once either environment is running, navigate to the app in your browser and use the upload form to select and submit an image file. After uploading, the image URL will be returned and you can view it directly in the browser.

---

## Services

| Service    | Role                                              |
|------------|---------------------------------------------------|
| `web`      | Flask application (dev) / Gunicorn WSGI (prod)   |
| `db`       | PostgreSQL database                               |
| `nginx`    | Reverse proxy and static/media file server (prod)|

---

## Repository Structure

```
flask-on-docker/
├── .github/
│   └── workflows/
│       └── main.yml            # GitHub Actions CI workflow
├── services/
│   ├── nginx/
│   │   ├── Dockerfile          # Nginx Dockerfile
│   │   └── nginx.conf          # Nginx configuration
│   └── web/
│       ├── project/            # Flask app source code
│       │   ├── __init__.py
│       │   ├── config.py
│       │   ├── media/
│       │   │   └── .gitkeep    # Keeps empty media dir in version control
│       │   └── static/
│       │       └── hello.txt
│       ├── Dockerfile          # Dev Dockerfile
│       ├── Dockerfile.prod     # Production Dockerfile
│       ├── entrypoint.sh       # Dev entrypoint script
│       ├── entrypoint.prod.sh  # Production entrypoint script
│       ├── manage.py           # DB management commands
│       └── requirements.txt
├── .env.dev                    # Development environment variables
├── .gitignore                  # Excludes .env.prod.db and other secrets
├── docker-compose.yml          # Development Compose config
├── docker-compose.prod.yml     # Production Compose config
└── README.md
```


> **Note:** The `.env.prod.db` file containing production database credentials is excluded from this repository via `.gitignore` and must be created locally before running the production stack.
