# Uptime Kuma

Uptime Kuma monitors HomeLab service availability and sends alerts.

## Deployment

The public Compose file mirrors the current deployment layout:

- image: `louislam/uptime-kuma:2`
- host port: `3001`
- persistent data: `/srv/docker/data/uptime-kuma`
- restart policy: `unless-stopped`

Start the service with:

```bash
docker compose up -d
```

Validate the Compose file before deployment:

```bash
docker compose config
```

## What is not stored here

The application database, monitor definitions, users, sessions, notification credentials and Discord/webhook secrets live under the persistent data directory and are intentionally excluded from Git.

This repository stores the deployment recipe, not the runtime state.
