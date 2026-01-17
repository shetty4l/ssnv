# Installation

## One-line install (Tier 1)
curl -fsSL https://raw.githubusercontent.com/shetty4l/ssnv/main/scripts/install.sh | sh

Tier 1 distros:
- Ubuntu 22.04
- Debian 12
- Raspberry Pi OS Bookworm

## What the installer does
- Installs apt prerequisites (including OpenH264)
- Creates the ssnv user and directories under /opt/ssnv
- Installs and enables systemd services
- Downloads signed release artifacts and verifies integrity

## Filesystem layout
- /opt/ssnv/releases/<app_version>
- /opt/ssnv/runtime/<runtime_version>
- /opt/ssnv/current (active app)
- /opt/ssnv/runtime/current (active runtime)
- /etc/ssnv
- /var/lib/ssnv

## Uninstall
- systemctl disable --now ssnvd ssnvd-update.timer
- rm -rf /opt/ssnv /etc/ssnv /var/lib/ssnv

## Disable auto-updates
Set update.enabled to false in /etc/ssnv/config.json and restart ssnvd.
