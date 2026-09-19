# Architecture

The HomeLab is built around a small Proxmox host and separates infrastructure roles by guest.

## Physical layer

```text
Internet
  |
Home Router
  |
2.5 GbE Switch
  |-- Main PC
  '-- GEEKOM A6
       '-- Proxmox VE
```

## Virtualization layer

The Proxmox host currently carries three main roles:

- **NAS LXC** — Debian + Samba for shared storage.
- **UniFi LXC** — network-management stack.
- **docker-host VM** — Debian VM dedicated to Docker workloads.

This separation keeps storage, network control and general application services independent.

## Docker services

The Docker VM currently hosts:

- **Uptime Kuma** for availability monitoring and alerting.
- **Dockge** for Compose stack visibility and management.
- **Forgejo** for self-hosted Git.

Compose files are intended to remain the declarative source of truth.

## Design goals

1. Small operational footprint.
2. Easy recovery and rebuild.
3. Clear boundaries between roles.
4. No application state committed to Git.
5. Incremental growth without requiring a full redesign.
6. Public documentation that does not expose secrets or personal data.

## Addressing

Exact production addresses are intentionally omitted from the public repository.

Documentation should use examples such as:

```text
gateway:      192.168.x.1
proxmox:      192.168.x.2
nas:          192.168.x.3
docker-host:  192.168.x.4
```

These are illustrative placeholders, not authoritative deployment values.
