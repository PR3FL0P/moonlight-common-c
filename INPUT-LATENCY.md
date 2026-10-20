# Private mouse receipt ACK protocol, version 1

This experimental extension measures the time from a client mouse event to
receiving the host's receipt acknowledgement. It does not measure one-way latency,
Windows input injection, game processing, or display response.

Sunshine advertises `a=x-ss-input-latency:1` in its RTSP DESCRIBE response. Only
Sunshine hosts advertising this exact version receive wrapped mouse input.
Stock hosts receive their usual mouse packets without extra measurement traffic.

A sampled mouse packet is sent on the existing reliable mouse channel and uses
an input header with magic `0x550000F0`. Its payload contains the client event
timestamp (LE64, in `LiGetMicroseconds()` units), followed by the entire original
mouse packet. The outer size follows the normal big-endian input-size convention.

The host validates the envelope and mouse packet, submits the original packet to
its input queue, and sends control message `0x55F0` using existing control-stream
encryption. Its 12-byte payload contains the original timestamp (LE64) and original
mouse packet magic (LE32). The client computes elapsed time using its own clock.

Every button transition is sampled. Mouse movement is sampled at most every
100 ms. Relative and absolute motion are supported. The host rejects malformed,
nested, non-mouse, truncated, and oversized envelopes.

The timestamped mouse APIs preserve the existing mouse APIs and allow clients to
include local event queue time. For a movement batch, pass the oldest event time.
`LiGetMouseAckStats()` returns independent button/motion statistics, or false when
the host lacks support. Call it only during an active stream, before stopping the
connection. Zero samples means the host supports the extension but has not yet
acknowledged input. Old replies cannot replace a newer sample.

Companion repositories:

- https://github.com/PR3FL0P/moonlight-qt/tree/input-latency
- https://github.com/PR3FL0P/Sunshine/tree/input-latency

The client release build and isolated protocol tests passed on Linux, including
Clang AddressSanitizer and UndefinedBehaviorSanitizer. A full Windows host build
and live streaming session remain unverified.
