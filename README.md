# Moonlight Streaming Core Library

This fork adds a negotiated, private mouse receipt ACK extension for the
[Moonlight input-latency client](https://github.com/PR3FL0P/moonlight-qt/tree/input-latency).
It requires the [matching Sunshine fork](https://github.com/PR3FL0P/Sunshine/tree/input-latency)
for receipt measurements. See [protocol documentation](INPUT-LATENCY.md).

Moonlight-common-c contains the core GameStream client code shared between [Moonlight](https://moonlight-stream.org) clients, including [Moonlight PC](https://github.com/moonlight-stream/moonlight-qt), [Moonlight Android](https://github.com/moonlight-stream/moonlight-android), [Moonlight iOS](https://github.com/moonlight-stream/moonlight-ios), and [Moonlight Chrome](https://github.com/moonlight-stream/moonlight-chrome).

If you are implementing your own Moonlight game streaming client that can use a C library, you probably want the code here.

## Note to Developers

Moonlight-common-c requires the _specific_ version of ENet that is bundled as a submodule. This version has changes required for IPv6 compatibility and retransmission reliability, among other things. These are breaking API/ABI changes which make Moonlight-common-c incompatible with other versions of the ENet library. Attempting to runtime link to another libenet library will cause your client to crash when connecting to recent versions of GeForce Experience.
