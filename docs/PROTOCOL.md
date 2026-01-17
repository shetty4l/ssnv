# Protocol (v1)

This document is a normative specification for ssnv v1. Unless otherwise stated:
- All integer fields are little-endian.
- All sizes are in bytes.
- Keywords MUST/SHOULD/MAY are used as defined in RFC 2119.

## 1. Transport
- ssnv uses QUIC for all transport.
- Control channel: a single bidirectional QUIC stream.
- Media channels: QUIC DATAGRAM frames.
- QUIC/TLS 1.3 provides encryption and integrity; ssnv performs application-level identity authorization.

Rationale: QUIC datagrams avoid head-of-line blocking for video and cursor updates.

## 2. Versioning
- Protocol version is a 16-bit unsigned integer.
- The version is sent in ClientHello and ServerHello.
- Datagrams carry a protocol version in their header.
- If a peer receives an unsupported major version, it MUST close the connection.

## 3. Control stream framing
Each control message is framed as:

- magic: u32 = 0x53534E56 ("SSNV")
- type: u16
- version: u16
- length: u32 (payload length in bytes)
- payload: [length] bytes

Unknown message types MUST be ignored if the connection remains valid.

### 3.1 Control message types
Types are u16. This table defines v1 messages.

- 0x0001 CLIENT_HELLO
- 0x0002 SERVER_HELLO
- 0x0003 AUTH_PROOF
- 0x0004 AUTH_RESULT
- 0x0005 START_SESSION
- 0x0006 INPUT_EVENT
- 0x0007 STATS_REPORT
- 0x0008 REQUEST_KEYFRAME
- 0x0009 SHUTDOWN

### 3.2 CLIENT_HELLO payload
- protocol_version: u16
- client_pubkey_ed25519: [32]
- device_name_len: u16
- device_name: [device_name_len]
- caps_len: u16
- caps: [caps_len] (TLV list)

### 3.3 SERVER_HELLO payload
- protocol_version: u16
- server_pubkey_ed25519: [32]
- session_id: u64
- selected_caps_len: u16
- selected_caps: [selected_caps_len] (TLV list)

### 3.4 AUTH_PROOF payload
- client_signature_ed25519: [64]

The signature is over:
- magic string "ssnv-auth-v1" (ASCII)
- client_pubkey_ed25519
- server_pubkey_ed25519
- session_id

### 3.5 AUTH_RESULT payload
- ok: u8 (0 = reject, 1 = accept)
- reason_len: u16
- reason: [reason_len]

### 3.6 START_SESSION payload
- mode: u8 (0 = performance, 1 = fidelity)
- initial_bitrate_kbps: u32
- initial_width: u16
- initial_height: u16

### 3.7 INPUT_EVENT payload
- event_type: u8 (0 = mouse_move, 1 = mouse_button, 2 = key)
- timestamp_us: u64
- payload_len: u16
- payload: [payload_len]

Mouse move payload:
- dx: i32
- dy: i32

Mouse button payload:
- button: u8
- state: u8 (0 = up, 1 = down)

Key payload:
- key_code: u16
- state: u8 (0 = up, 1 = down)

### 3.8 STATS_REPORT payload
- rtt_ms: u16
- loss_ppm: u32
- jitter_ms: u16
- decode_queue_ms: u16
- render_fps_x100: u16

### 3.9 REQUEST_KEYFRAME payload
- track_id: u32

### 3.10 SHUTDOWN payload
- reason_code: u16
- reason_len: u16
- reason: [reason_len]

## 4. Capabilities TLV
Capabilities are encoded as TLVs:
- cap_type: u16
- cap_len: u16
- cap_value: [cap_len]

Defined capability types:
- 0x0001 SUPPORTED_TRACKS (u32 bitset)
- 0x0002 SUPPORTED_CODECS (u32 bitset)
- 0x0003 MAX_DATAGRAM_SIZE (u16)
- 0x0004 CURSOR_TRACK (u8, 0/1)

## 5. Datagram header
All datagrams share a common header:

- magic: u32 = 0x53534E56 ("SSNV")
- proto_ver: u16
- track_type: u8 (0 = video, 1 = cursor, 2 = audio)
- flags: u8
- session_id: u64
- track_id: u32
- seq_no: u32
- timestamp_us: u64
- unit_id: u32
- frag_index: u16
- frag_count: u16

Flags:
- 0x01 KEYFRAME
- 0x02 START_OF_UNIT
- 0x04 END_OF_UNIT

Datagrams may arrive out of order or be dropped. Receivers MUST tolerate loss.

## 6. Video payload (track_type = 0)
- Codec: H.264
- Format: Annex B byte stream
- Color: 8-bit 4:2:0
- SPS/PPS: MUST be sent before each IDR (either inline or as a dedicated access unit).

Rationale: Annex B is widely supported and easy to transport without container metadata.

## 7. Cursor payload (track_type = 1)
Cursor payload is position-only in v1.

- x_px: i32
- y_px: i32

Coordinates are absolute pixels in the current encoded frame space.
Senders MUST clamp coordinates to [0..width-1] and [0..height-1].

## 8. Notes on extensions
- New tracks MUST allocate a new track_type and define payload format.
- New control messages MUST use new type ids and SHOULD include a versioned payload.
