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

## What ssnv is
- Full-display remote desktop
- Secure by default (auth + encryption)
- Headless-capable appliance mode
- Single-user, single-session focused
- Evolvable toward WAN/cloud usage

## What ssnv is not
- Not a general-purpose VNC/RDP replacement
- Not window-semantic (window capture comes later via client-side crop)
- Not multi-user in v1

## Docs
- Architecture: ARCHITECTURE.md
- Protocol: PROTOCOL.md
- Distribution: DISTRIBUTION.md
- Installation: INSTALL.md
- Security: SECURITY.md
- Usage: USAGE.md
- Troubleshooting: TROUBLESHOOTING.md
- Milestones: MILESTONES.md
- Modules: MODULES.md
