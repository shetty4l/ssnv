# Troubleshooting

## Common issues
- Black screen: capture backend failed or DRM permissions missing
- No input: /dev/uinput permissions or input service not running
- Cursor missing: cursor track disabled or client overlay not rendering
- Encoder fallback: expected if hardware encoder unavailable
- Updates failing: manifest verification or health check failure

## Commands
ssnvd doctor
ssnvd status --json

## Notes
- Appliance mode (Pi HDMI) assumes ssnvd owns the display session.
- If updates roll back, check journald for ssnvd-update.service logs.
