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
- Sanitized public Compose definitions published for Uptime Kuma, Dockge, Forgejo, Homepage, Beszel and n8n.
- H09 AI Agent Stack local coding pipeline operational with Hermes, OpenCode, deterministic routing, verifier, protected-file policy, persistent audit, rollback and automatic local-fast → local-heavy escalation.

## In progress / next

- Complete the UniFi controller deployment.
- Add secure remote access.
- Mature NAS storage and monitoring.
- Define backup policy for services and important data.
- Document recovery procedures.
- Add infrastructure diagrams as the architecture grows.
- Extend reproducible configuration beyond Docker where it provides clear value.
- Exercise H09 Executor V0.1 on real development tasks and improve Hermes task/result presentation.
- Design cloud escalation only after enough real-world evidence is collected from the validated local pipeline.

## Later

- Network segmentation where useful.
- Multi-disk storage and redundancy evaluation.
- More self-hosted services where they solve a real need.
- Infrastructure-as-code where it improves reproducibility rather than adding complexity for its own sake.
