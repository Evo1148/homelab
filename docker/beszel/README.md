# Beszel

Beszel provides lightweight infrastructure and container monitoring for the HomeLab.

## Deployment

The public Compose definition mirrors the current HomeLab deployment while keeping machine-specific addressing and agent secrets outside Git.

Current layout:

- Beszel Hub: `henrygd/beszel:0.19.0`
- Beszel Agent: `henrygd/beszel-agent:0.19.0`
- Hub port: configurable through `BESZEL_PORT`
- Hub application URL: configurable through `BESZEL_APP_URL`
- Hub data: `/srv/docker/data/beszel/hub`
- Agent data: `/srv/docker/data/beszel/agent`
- Shared Unix socket: `/srv/docker/data/beszel/socket`
- Agent uses host networking
- Docker socket is mounted read-only for container visibility

Create the public environment file:

```bash
cp .env.example .env
```

The agent has a separate local environment file:

```bash
cp agent.env.example agent.env
```

Populate `agent.env` only with the variables required by the live Beszel pairing/configuration. The real file must remain local.

Validate and start:

```bash
docker compose config
docker compose up -d
```

## Security notes

The agent runs with `network_mode: host` and mounts `/var/run/docker.sock` read-only. Read-only prevents direct writes to the socket path, but Docker API access can still expose extensive information about containers and the host. Treat access to the Beszel agent and its configuration as sensitive.

The public repository intentionally excludes:

- the real `.env` file;
- the real `agent.env` file;
- pairing keys or tokens;
- Beszel runtime databases;
- monitoring history;
- generated state under `/srv/docker/data/beszel`.

This repository stores the deployment recipe, not the monitoring state or credentials.
