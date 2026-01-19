# justUp

justUp is a lightweight uptime monitor with a web UI, alerting, and PDF reports.

This README is written for users who run justUp via Docker (Docker Hub). No local Python setup required.

## Why justUp (USP)

- Signal integration for alerts/notifications
- Quality Score to track overall network/service quality at a glance
- High-quality PDF reports (charts + traceroute map) for audits and sharing
- Integrated traceroute (UI + PDF) to visualize the network path
- Fully responsive web UI (works well on desktop, tablet, mobile)

## What you get

- Checks: HTTP, Ping, TCP port, TLS certificate expiry
- Dashboard + charts
- Alerts + recipients
- Traceroute (UI + PDF map)
- PDF reports

## Requirements

- Docker Engine (and optionally Docker Compose)
- A persistent directory/volume for `/app/data`

## Quick start (Docker)

1) Create an encryption key (required)

```bash
docker run --rm python:3.12-slim \
  python -c "from cryptography.fernet import Fernet; print(Fernet.generate_key().decode())"
```

2) Run the container

Replace the image name with your Docker Hub image (example: `YOUR_DOCKERHUB_USER/justup:latest`).

```bash
docker run -d --name justup \
  -p 8080:8080 \
  -e TZ=UTC \
  -e PUID=1000 \
  -e PGID=1000 \
  -e LOG_LEVEL=INFO \
  -e UPMON_ENCRYPTION_KEY="<YOUR_FERNET_KEY>" \
  -v "$PWD/justup-data:/app/data" \
  --restart unless-stopped \
  YOUR_DOCKERHUB_USER/justup:latest
```

Open:

- UI: `http://localhost:8080/`
- Health: `http://localhost:8080/health`

## Default login

- Username: `admin`
- Password: `admin`

Change the password immediately after first login.

## Docker Compose (recommended)

Create a `docker-compose.yml` like this:

```yaml
services:
  justup:
    image: YOUR_DOCKERHUB_USER/justup:latest
    container_name: justup
    restart: unless-stopped
    ports:
      - "8080:8080"
    environment:
      TZ: "UTC"
      PUID: "1000"
      PGID: "1000"
      LOG_LEVEL: "INFO"
      UPMON_ENCRYPTION_KEY: "<YOUR_FERNET_KEY>"
      # Optional paths (defaults are fine when you mount /app/data)
      UPMON_DATA_DIR: "data/"
      UPMON_DB_PATH: "data/sqlite/app.sqlite3"
      UPMON_TSDB_DIR: "data/tsdb"
    volumes:
      - ./justup-data:/app/data
    healthcheck:
      test: ["CMD", "curl", "-fsS", "http://localhost:8080/health"]
      interval: 30s
      timeout: 5s
      retries: 3
      start_period: 10s
```

Start:

```bash
docker compose up -d
```

## Updating

Before updating, it’s recommended to back up your data directory (especially `data/sqlite/app.sqlite3`).

```bash
docker compose pull
docker compose up -d
```

## Data persistence (important)

All runtime state lives under `/app/data`:

- SQLite DB: users/settings/targets
- TSDB: metrics data
- Generated reports

If you don’t mount `/app/data`, you’ll lose everything on container recreation.

## Configuration reference (most used)

| Variable | Required | Default | Meaning |
|---|---:|---|---|
| `UPMON_ENCRYPTION_KEY` | yes | — | Encrypts secrets at rest (Fernet) |
| `LOG_LEVEL` | no | `INFO` | Logging verbosity |
| `TZ` | no | `UTC` | Container timezone |
| `PUID` / `PGID` | no | `1000` | File ownership for `/app/data` |
| `JUSTUP_HOST` | no | `0.0.0.0` | Bind address |
| `JUSTUP_PORT` | no | `8080` | Listen port |
| `UPMON_QUALITY_TARGETS` | no | — | Comma-separated hosts used by the “quality probe” |

## Troubleshooting

### Container starts but can’t write data

- Ensure the host directory you mount to `/app/data` is writable.
- Use `PUID`/`PGID` matching the directory owner.

### “Missing encryption key” / startup aborts

- `UPMON_ENCRYPTION_KEY` is mandatory.
- Keep it stable across upgrades. Changing it can make stored secrets unreadable.

### Healthcheck

- `GET /health` should return `200 OK`.
- Logs: `docker logs -f justup`
