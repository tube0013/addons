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

### Option: `baudrate`

The serial baudrate of the RCP firmware. The default (460800) matches the
firmware shipped for the ZBT-1/ZBT-2.

### Option: `flow_control`

The serial flow control mode (`hardware`, `software`, `none`). The RCP UART
drops bytes under load without hardware flow control, corrupting frames; only
change this if the radio's firmware was built without it.

### Option: `log_level`

The stack's logging verbosity (`error`, `warn`, `info`, `debug`, `trace`).
`debug` logs every frame on the network and is very chatty; `info` is
appropriate for normal operation.

## Running standalone (plain Docker)

The same image runs outside Home Assistant as an ordinary container. When
`/data/options.json` is absent, configuration is read from environment variables
instead of the add-on options:

<table>
<tr><th><code>docker run</code></th><th><code>docker-compose.yml</code></th></tr>
<tr><td valign="top">

```bash
docker run --rm \
  --device /dev/serial/by-id/usb-...-if00-port0 \
  --cap-add SYS_NICE \
  -p 9999:9999 \
  -e ZIGGURAT_DEVICE=/dev/serial/by-id/usb-...-if00-port0 \
  -e ZIGGURAT_BAUDRATE=460800 \
  -e ZIGGURAT_FLOW_CONTROL=hardware \
  -e ZIGGURAT_LOG_LEVEL=info \
  ghcr.io/zigpy/ziggurat-addon
```

</td><td valign="top">

```yaml
services:
  ziggurat:
    image: ghcr.io/zigpy/ziggurat-addon
    restart: unless-stopped
    devices:
      - /dev/serial/by-id/usb-...-if00-port0
    cap_add:
      - SYS_NICE
    ports:
      - "9999:9999"
    environment:
      ZIGGURAT_DEVICE: /dev/serial/by-id/usb-...-if00-port0
      ZIGGURAT_BAUDRATE: "460800"
      ZIGGURAT_FLOW_CONTROL: hardware
      ZIGGURAT_LOG_LEVEL: info
```

</td></tr>
</table>

`--cap-add SYS_NICE` lets the stack raise its scheduling priority. It is optional but
recommended.
