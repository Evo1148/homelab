# Homepage

Homepage provides a self-hosted dashboard for the HomeLab.

## Deployment

The public Compose file mirrors the real deployment pattern while keeping machine-specific addressing outside Git:

- image: `ghcr.io/gethomepage/homepage:v2.4.0`
- configurable host port
- persistent configuration under `/srv/docker/data/homepage/config`
- `PUID` / `PGID` supplied through environment variables
- `HOMEPAGE_ALLOWED_HOSTS` parameterized instead of publishing the live LAN address

Create a local environment file:

```bash
cp .env.example .env
```

Then replace the example hostname with the real internal hostname or host:port used by the HomeLab.

Validate and start:

```bash
docker compose config
docker compose up -d
```

## What is not stored here

The repository intentionally excludes the real Homepage configuration directory because it may contain internal service URLs, integration settings, credentials or other environment-specific data.

Do not commit:

- the real `.env` file;
- API keys or service tokens;
- private internal URLs that are not needed publicly;
- runtime configuration copied from `/srv/docker/data/homepage/config`.

This repository stores the deployment recipe, not the full dashboard state.
