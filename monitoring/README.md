# Monitoring

The HomeLab currently uses two complementary monitoring services:

- **Uptime Kuma** for service availability checks and alerting.
- **Beszel** for lightweight host and container monitoring.

Their sanitized Docker deployment definitions live under:

- [docker/uptime-kuma](../docker/uptime-kuma/)
- [docker/beszel](../docker/beszel/)

Runtime databases, monitoring history, notification credentials and agent pairing secrets remain outside Git.
