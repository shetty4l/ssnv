# Architecture

ssnv consists of a Linux server daemon (ssnvd) and a macOS client.

## System planes
- Media plane: QUIC DATAGRAM tracks for video and cursor
- Control plane: QUIC stream for handshake, input, and feedback
- Admin plane: local unix socket for status/doctor/update gating
- Storage: trusted devices, identity keys, and update state

## Server components
- Listener: accepts QUIC connections
- Session manager: authorizes clients and enforces single active session policy
- Session worker:
  - video loop: capture -> encode -> packetize -> datagram send
  - cursor loop: cursor sampling -> datagram send
  - control loop: input receive -> inject; feedback -> rate control
- Rate controller: adapts bitrate/IDR based on stats and queue depth
- Admin server: /run/ssnv/admin.sock for health and diagnostics

## Client components (macOS)
- QUIC session and control stream
- Video depacketize/decode (VideoToolbox)
- Fullscreen Metal renderer
- Cursor overlay (client-side default cursor)
- Input capture and forwarding

## Capture backends
- Bring-up backend: X11/Xvfb or synthetic frames for early integration
- Appliance backend: DRM/KMS for Pi HDMI usage
- Desktop backend (planned): PipeWire/Wayland screencast capture

## Encoder backends
- V1 baseline: OpenH264 (installed via apt)
- Planned: VAAPI/NVENC/V4L2 M2M hardware encoders

## Session model
- Multiple trusted devices per server
- Single active session enforced by policy (protocol supports multiple)

## Design principles
- Stay-live policy: bounded queues, drop frames under pressure
- Separate cursor track for responsiveness
- Protocol and transport are explicit and extensible
