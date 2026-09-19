# Roadmap

This roadmap reflects the current direction of the HomeLab. Items are promoted into the documented baseline only after validation.

## Validated baseline

- Proxmox VE host installed and operational.
- LXC and VM lifecycle tested.
- Snapshot and backup/restore workflow tested.
- Debian NAS guest with Samba access operational.
- Dedicated Debian Docker VM operational.
- Docker Engine and Compose operational.
- Uptime Kuma operational.
- Dockge operational.
- Forgejo operational with SSH authentication and real clone → commit → push validation.

## In progress / next

- Complete the UniFi controller deployment.
- Add secure remote access.
- Mature NAS storage and monitoring.
- Define backup policy for services and important data.
- Add sanitized Compose examples to this repository.
- Document recovery procedures.
- Add infrastructure diagrams as the architecture grows.

## Later

- Network segmentation where useful.
- Multi-disk storage and redundancy evaluation.
- More self-hosted services where they solve a real need.
- Infrastructure-as-code where it improves reproducibility rather than adding complexity for its own sake.
