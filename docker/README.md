# Docker

Docker workloads run on a dedicated Debian VM.

This directory contains **sanitized, reproducible Compose definitions** for the current public HomeLab service set. Runtime data, databases, credentials and backups stay outside Git.

## Published stacks

| Service | Role | Public deployment |
| --- | --- | --- |
| [Uptime Kuma](./uptime-kuma/) | Availability monitoring and alerts | `compose.yml` |
| [Dockge](./dockge/) | Docker Compose management | `compose.yml` + `.env.example` |
| [Forgejo](./forgejo/) | Self-hosted Git | `compose.yml` + `.env.example` |
| [Homepage](./homepage/) | HomeLab dashboard | `compose.yml` + `.env.example` |
| [Beszel](./beszel/) | Host/container monitoring | `compose.yml` + environment templates |
| [n8n](./n8n/) | Workflow automation | `compose.yml` + `.env.example` |

## Convention

Each stack keeps the deployment recipe in Git while excluding its live state.

Typical workflow:

```bash
cp .env.example .env   # when required
docker compose config
docker compose up -d
```

Never commit real `.env` files, agent credentials, databases, notification tokens or application data.
