# Proxmox

This directory is reserved for sanitized Proxmox documentation, templates and reproducible configuration.

Do not commit full host backups, cluster credentials, private keys or machine-specific exports containing secrets.

## Current role split

- NAS: LXC
- UniFi controller stack: LXC
- General application hosting: dedicated Debian VM running Docker
