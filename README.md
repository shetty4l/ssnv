# ssnv

ssnv is a secure, opinionated remote desktop system designed for robustness, correctness, and minimal configuration.

It provides encrypted, authenticated remote display and input for Linux hosts, with a macOS client, and is designed to run reliably on laptops, headless machines (e.g. Raspberry Pi), and future cloud instances (e.g. EC2).

## V1 scope
- Single active session per server; multiple trusted client devices
- LAN/VPN only (no public exposure or broker in v1)
- macOS client is fullscreen-only
- Cursor is a separate track (position-only); audio deferred
- H.264 video (Annex B on wire)
- Tier 1 distros: Ubuntu 22.04, Debian 12, Raspberry Pi OS Bookworm

## Quickstart
curl -fsSL https://raw.githubusercontent.com/shetty4l/ssnv/main/scripts/install.sh | sh

## Docs
- Architecture: docs/ARCHITECTURE.md
- Protocol: docs/PROTOCOL.md
- Distribution: docs/DISTRIBUTION.md
- Installation: docs/INSTALL.md
- Security: docs/SECURITY.md
- Usage: docs/USAGE.md
- Troubleshooting: docs/TROUBLESHOOTING.md
- Milestones: docs/MILESTONES.md
- Modules: docs/MODULES.md
