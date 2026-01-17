# Distribution

This document specifies how ssnv is packaged, installed, updated, and rolled back in v1.

## 1. Supported platforms (v1)
- Ubuntu 22.04 (Tier 1)
- Debian 12 (Tier 1)
- Raspberry Pi OS Bookworm (Tier 1)

All v1 installs assume systemd and apt.

## 2. Install layout
- /opt/ssnv/releases/<app_version>/
- /opt/ssnv/current -> /opt/ssnv/releases/<app_version>
- /opt/ssnv/runtime/<runtime_version>/lib/
- /opt/ssnv/runtime/current -> /opt/ssnv/runtime/<runtime_version>
- /etc/ssnv/config.json
- /var/lib/ssnv/
- /run/ssnv/admin.sock

## 3. Artifacts
Each release publishes:
- ssnv-linux-x86_64-<app_version>.tar.gz
- ssnv-linux-aarch64-<app_version>.tar.gz
- ssnv-runtime-linux-x86_64-<runtime_version>.tar.gz
- ssnv-runtime-linux-aarch64-<runtime_version>.tar.gz
- manifest.json
- manifest.sha256
- manifest.sig

Runtime bundles contain msquic and its TLS provider libraries.

## 4. Manifest schema
manifest.json is UTF-8 JSON with this schema:

- channel: string
- app:
  - version: string
  - artifacts:
    - x86_64:
      - url: string
      - sha256: string
    - aarch64:
      - url: string
      - sha256: string
- runtime:
  - version: string
  - artifacts:
    - x86_64:
      - url: string
      - sha256: string
    - aarch64:
      - url: string
      - sha256: string
- min_supported:
  - ubuntu: string
  - debian: string
  - rpi_os: string

manifest.sha256 is the hex SHA-256 digest of manifest.json.
manifest.sig is an Ed25519 signature over the raw bytes of manifest.sha256.

## 5. Update policy
- Auto-updates are enabled by default.
- Updates run daily with jitter.
- Updates MUST skip when active_session is true.
- Updates MUST verify manifest.sig and artifact hashes before install.

## 6. Update algorithm
1) Query /run/ssnv/admin.sock for status.
2) If active_session is true, exit successfully.
3) Download manifest.json, manifest.sha256, manifest.sig.
4) Verify manifest.sha256 and manifest.sig.
5) Download app and runtime artifacts for the local architecture.
6) Verify artifact hashes.
7) Extract into /opt/ssnv/releases/<app_version> and /opt/ssnv/runtime/<runtime_version>.
8) Atomically repoint /opt/ssnv/current and /opt/ssnv/runtime/current.
9) Restart ssnvd.
10) Perform a health check via /run/ssnv/admin.sock.
11) If health check fails, roll back to previous symlinks and restart.

## 7. systemd units
- ssnvd.service: runs the daemon as user ssnv.
- ssnvd-update.service: oneshot update runner.
- ssnvd-update.timer: daily update schedule with jitter.

## 8. Configuration
/etc/ssnv/config.json may override:
- update.enabled (bool)
- update.base_url (string)
- update.channel (string)
- update.jitter_seconds (number)

## 9. Health check contract
The admin socket must return status with:
- app_version
- runtime_version
- active_session
- listener_ok
- quic_ok

If any required field is missing or false, the update MUST roll back.
