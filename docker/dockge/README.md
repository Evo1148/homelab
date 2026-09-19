# Dockge

Dockge provides a web interface for Docker Compose stacks.

Compose files remain the source of truth; Dockge is treated as an operational interface rather than the authoritative configuration store.

## Deployment

The public Compose definition mirrors the HomeLab deployment pattern:

- image: `louislam/dockge:1`
- web port: `5001`
- application data: `/srv/docker/data/dockge`
- stack directory: `/srv/docker/compose`
- Docker socket mounted for container/stack management
- UID/GID supplied through environment variables

Create the local environment file from the example:

```bash
cp .env.example .env
```

Then set `PUID` and `PGID` to the account that should own Dockge-managed files:

```bash
id -u
id -g
```

Validate and start:

```bash
docker compose config
docker compose up -d
```

## Security note

Dockge mounts `/var/run/docker.sock`. Access to the Docker socket is effectively privileged access to the Docker host, so the Dockge web interface should not be exposed publicly without an appropriate authentication and network-access strategy.

## What is not stored here

The repository intentionally excludes:

- the real `.env` file;
- Dockge runtime data;
- service databases and logs;
- credentials or tokens from managed stacks.

This repository stores the deployment recipe, while runtime state remains on the HomeLab.
