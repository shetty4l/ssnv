# Security

## Threat model
- Untrusted LAN
- Accidental port exposure
- Malicious local clients

## Guarantees
- Mandatory authentication and authorization
- Mandatory encryption (QUIC/TLS 1.3)
- No downgrade paths

## Trusted devices
- Multiple trusted client devices per server
- Pairing enrolls client public keys with device names
- Non-interactive reconnect for trusted devices
- Revocation is required via device management commands

## Update security
- Release artifacts are verified with Ed25519 signatures
- Updates roll back on failed health checks

## Cryptography
- QUIC/TLS 1.3 for transport security
- Ed25519 for identity and update signatures
- SHA-256 for artifact hashes

## V1 scope
- LAN/VPN only (no public exposure)
- Single active session per server

## Hardening (v1)
- systemd sandboxing and least privilege
- Scoped access to /dev/dri and /dev/uinput
