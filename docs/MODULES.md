# Modules

## Server
- transport: QUIC/msquic wrapper, streams + datagrams
- proto: control framing and datagram headers
- session: authorization, session manager, session worker
- capture: backend interface and implementations
- encode: encoder interface and implementations
- input: uinput injection
- admin: unix socket status/doctor/update gating
- store: trusted devices and identity keys
- update: manifest verification and update state machine
- metrics: counters and telemetry

## Client
- transport: QUIC session and stream/datagram handling
- proto: control framing and datagram headers
- decode: H.264 decode
- render: fullscreen Metal renderer
- input: input capture and forwarding
- ui: minimal session UI and status
