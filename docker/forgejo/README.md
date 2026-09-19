# Forgejo

Forgejo provides self-hosted Git inside the HomeLab.

The deployment has been validated end-to-end with SSH authentication and a real `clone → commit → push` workflow.

## Deployment

The public Compose definition preserves the real deployment model while moving machine-specific addressing into environment variables.

Current characteristics:

- image: `codeberg.org/forgejo/forgejo:15.0.9`
- SQLite database
- public registration disabled
- HTTP exposed through a configurable host port
- SSH exposed through a configurable host port
- persistent application data under `/srv/docker/data/forgejo`
- host timezone mounted read-only

Create a local environment file:

```bash
cp .env.example .env
```

Then replace the example hostnames with the real internal hostname or address used by your HomeLab.

Validate and start:

```bash
docker compose config
docker compose up -d
```

## Why the address is parameterized

The live deployment uses a private LAN address. The public repository deliberately avoids publishing environment-specific network details where they are not needed for reproducibility.

The Compose file therefore uses:

- `FORGEJO_DOMAIN`
- `FORGEJO_ROOT_URL`
- `FORGEJO_SSH_DOMAIN`
- `FORGEJO_HTTP_PORT`
- `FORGEJO_SSH_PORT`

## What is not stored here

Never commit:

- the real `.env` file;
- Forgejo user tokens;
- SSH private keys;
- the SQLite database;
- repositories stored under Forgejo data;
- sessions, secrets or generated configuration;
- backups of `/srv/docker/data/forgejo`.

The Git repository contains the deployment recipe, not the Forgejo runtime state.
