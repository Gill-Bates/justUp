# justUp

[![Docker](https://img.shields.io/badge/Docker-gill--bates%2Fjustup-2496ED?logo=docker&logoColor=white)](https://hub.docker.com/r/gill-bates/justup)
[![License](https://img.shields.io/badge/License-MIT-green.svg)](LICENSE)
[![Version](https://img.shields.io/badge/Version-1.1.0-blue.svg)](https://github.com/gill-bates/justup/releases)

Seriously – why another monitoring tool?

**Because I wanted something that actually fits.**

justUp is a self-contained, Docker-first monitoring solution for websites and endpoints, designed to be simple to operate, efficient in storage, and professional in output — without external databases, agents, or Grafana stacks.

---

## What makes justUp different

- **Truly self-contained**  
  No Postgres, no InfluxDB, no Redis. justUp ships with its own SQLite + resampled TSDB and runs as a single container.

- **Standalone binary**  
  Built with Nuitka. No Python runtime, no pip, no virtualenv in production.

- **Server-rendered UI (no SPA)**  
  HTMX-based, fast, predictable, and SEO/ops-friendly. No React/Vue overhead.

- **Meaningful metrics, not noise**  
  One primary uptime signal per target, with auxiliary checks for context.

- **Professional reporting**  
  High-quality PDF reports with charts, downtime tables, and traceroute maps.

---

## Features

### Monitoring & Checks
- HTTP (HEAD)
- Ping (ICMP)
- TCP port checks
- TLS certificate expiration

> Uptime is derived from **exactly one primary check** per target  
> (HTTP → Ping → TCP, in that order). Other checks provide context, not ambiguity.

---

### Dashboard & UI
- Clean, responsive web UI (desktop, tablet, mobile)
- Grouped targets with live status
- Lazy-loaded metrics (no heavy queries on page load)
- Optional UTC display for consistent ops views

---

### Alerts & Notifications
- Reliable alerting with escalation support
- Signal integration (extensible by design)
- Recipients and per-target routing
- Acknowledgement workflow

---

### Quality Score (Monitoring the Monitor)
justUp continuously evaluates **its own Internet connection quality** using a weighted set of external probe targets.

If the monitoring environment itself becomes unreliable:
- Targets are marked as `unknown`
- False positives are avoided
- Overall trust in alerts is preserved

This is not just a score — it is a **quality gate** for monitoring integrity.

---

### Time Series Database with Resampling
Instead of storing raw data forever:

- Recent data: full resolution
- Older data: automatically resampled
- Trends, averages, and extremes preserved
- Storage stays compact and fast

No manual cleanup. No data loss where it matters.

---

### Traceroute (UI + PDF)
- On-demand traceroute directly from the monitor
- Offline GeoIP (no external lookups)
- Interactive UI map
- High-resolution PDF rendering with controlled auto-zoom

---

### PDF Reports
- Charts, uptime statistics, downtime history
- Embedded traceroute maps
- Sync and async generation
- Rate-limited heavy operations
- Suitable for audits, customers, and documentation

---

## Requirements

- Docker Engine (Docker Compose recommended)
- Persistent volume for `/app/data`

---

## Quick Start (Docker)

### 1) Generate encryption key (required)

```bash
docker run --rm python:3.12-slim \
  python -c "from cryptography.fernet import Fernet; print(Fernet.generate_key().decode())"
```

### 2) Run container

```bash
docker run -d --name justup \
  -p 8080:8080 \
  -e PUID=1000 \
  -e PGID=1000 \
  -e LOG_LEVEL=INFO \
  -e UPMON_ENCRYPTION_KEY="<YOUR_FERNET_KEY>" \
  -v "$PWD/justup-data:/app/data" \
  --restart unless-stopped \
  gill-bates/justup:latest
```

Open:

- UI: http://localhost:8080/
- Health: http://localhost:8080/health

### Default Login

- Username: `admin`
- Password: `admin`

> **Change the password immediately after first login.**

---

## Docker Compose (recommended)

```yaml
services:
  justup:
    image: gill-bates/justup:latest
    container_name: justup
    restart: unless-stopped
    ports:
      - "8080:8080"
    environment:
      PUID: "1000"
      PGID: "1000"
      LOG_LEVEL: "INFO"
      UPMON_ENCRYPTION_KEY: "<YOUR_FERNET_KEY>"
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

---

## Data Persistence (important)

All runtime state lives under `/app/data`:

- SQLite database (users, settings, targets)
- Time series database (metrics)
- Generated PDF reports

> **Without a mounted volume, all data is lost on container recreation.**

---

## Configuration Reference (most used)

| Variable | Required | Default | Description |
|----------|:--------:|---------|-------------|
| `UPMON_ENCRYPTION_KEY` | yes | — | Encrypts secrets at rest (Fernet) |
| `LOG_LEVEL` | no | `INFO` | Logging verbosity |
| `PUID` / `PGID` | no | `1000` | File ownership for `/app/data` |
| `JUSTUP_HOST` | no | `0.0.0.0` | Bind address |
| `JUSTUP_PORT` | no | `8080` | Listen port |
| `UPMON_QUALITY_TARGETS` | no | predefined | Hosts used for quality probe |

---

## Troubleshooting

### Container starts but cannot write data

- Ensure the host directory mounted to `/app/data` is writable
- Match `PUID` / `PGID` to directory ownership

### Startup aborts with "missing encryption key"

- `UPMON_ENCRYPTION_KEY` is mandatory
- Keep it stable across upgrades

### Healthcheck

- `GET /health` must return `200 OK`
- Logs: `docker logs -f justup`
