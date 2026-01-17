# Usage

## Server
ssnvd start [--mode performance|fidelity]

## Pairing
ssnvd pair
ssnv pair <server-ip> <token> --name "device-name"

## Trusted devices
ssnvd devices list
ssnvd devices revoke <fingerprint>
ssnvd devices rename <fingerprint> <name>

## Client
ssnv connect <server-ip>

## Status and updates
ssnvd status --json
ssnvd update --check
ssnvd update --apply
