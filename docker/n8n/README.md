# n8n

n8n provides workflow automation inside the HomeLab.

The deployment uses a dedicated PostgreSQL container and keeps n8n application state in persistent storage.

## Deployment

Current public layout:

- n8n: `docker.n8n.io/n8nio/n8n:2.39.8`
- PostgreSQL: `postgres:18`
- n8n state: `/srv/docker/data/n8n/n8n`
- PostgreSQL state: `/srv/docker/data/n8n/postgres`
- PostgreSQL healthcheck gates n8n startup
- application URLs, ports and credentials are supplied through environment variables

Create the local environment file:

```bash
cp .env.example .env
```

Replace every placeholder secret before starting the stack.

Validate and start:

```bash
docker compose config
docker compose up -d
```

## Important secrets

### `POSTGRES_PASSWORD`

Use a strong random password and keep it only in the local `.env`.

### `N8N_ENCRYPTION_KEY`

This value protects credentials stored by n8n. It must remain secret **and stable**. Do not casually rotate or lose it, because existing encrypted credentials may become unreadable.

## HTTP / HTTPS

The current HomeLab deployment can operate over trusted-LAN HTTP.

For a reverse-proxied HTTPS deployment, update:

- `N8N_PROTOCOL`
- `N8N_EDITOR_BASE_URL`
- `WEBHOOK_URL`
- `N8N_SECURE_COOKIE=true`

The public Compose definition does not hard-code the live HomeLab address.

## What is not stored here

Never commit:

- the real `.env`;
- PostgreSQL credentials;
- `N8N_ENCRYPTION_KEY`;
- n8n API keys or integration credentials;
- workflow credential exports;
- PostgreSQL data;
- n8n runtime state;
- webhook secrets;
- backups of `/srv/docker/data/n8n`.

This repository contains the deployment recipe, not workflow secrets or runtime databases.
