# Home Assistant App: Ziggurat

## How it works

Ziggurat is a host-side Zigbee stack written in Rust: instead of delegating the Zigbee
protocol to closed-source coordinator firmware (EmberZNet, Z-Stack), the whole stack
runs in this app and drives the radio as a simple IEEE 802.15.4 transceiver over the
OpenThread Spinel protocol. Any stick flashed with OT-RCP firmware works, e.g. the Home
Assistant Connect ZBT-1/ZBT-2.

The app exposes a WebSocket API on port 9999. ZHA can connect to it via the
`zigpy-ziggurat` radio library.

The app is stateless: all network state (keys, frame counters, device tables) is
owned and persisted by ZHA, so the app can be rebuilt or reinstalled freely without
losing the network.

## Installation

1. Flash the radio with OpenThread RCP firmware (460800 baud, hardware flow control).
2. Install the app and select the radio under **Device**.
3. Start the app.
4. Configure ZHA with radio type `ziggurat` and device path
   `socket://local_ziggurat:9999`. An existing EmberZNet or Z-Stack network can be
   migrated by restoring its network backup.

## Configuration

### Option: `device` (required)

The serial port the 802.15.4 RCP radio is attached to.

### Option: `log_level`

The stack's logging verbosity (`error`, `warn`, `info`, `debug`, `trace`).
`debug` logs every frame on the network and is very chatty; `info` is
appropriate for normal operation.

## Building

The first build will take a while, especially on older hardware. Subsequent builds use a
build cache and will be very fast.
