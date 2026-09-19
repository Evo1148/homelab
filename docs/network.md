# Network

## Current design

The HomeLab uses a simple flat LAN today, with a 2.5 GbE switch connecting the main workstation and the Proxmox host to the home router.

Network-management services are being introduced incrementally rather than redesigning the LAN all at once.

## Principles

- Keep management access simple and recoverable.
- Prefer hostnames and documented roles over scattering hard-coded addresses.
- Do not publish production credentials, VPN keys or private SSH material.
- Introduce segmentation only when it solves a real operational or security need.
- Keep remote access separate from public Internet exposure.

## Public documentation convention

Use placeholders in examples:

```text
192.168.x.0/24
192.168.x.1
host.home.arpa
```

Do not commit router exports or controller backups unless they have been explicitly sanitized.
