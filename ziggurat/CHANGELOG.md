# Changelog

## 0.2.2-tcp-test

 - Switches the testing build to `tcp-adaptor-support-legacy-protocol` instead of
   `tcp-adaptor-support`: dev's tip (the ESP32 support merge, #29) changed the
   send_aps wire protocol to fire-and-forget + async notifications, which the
   currently-released zigpy-ziggurat doesn't understand yet and causes ZHA's startup
   permit(0) broadcast to hang. This branch has the same TCP-transport patch rebuilt
   on the pre-merge base instead, so it stays compatible with zigpy-ziggurat.

## 0.2.1-tcp-test

 - Testing build for [zigpy/ziggurat#39](https://github.com/zigpy/ziggurat/pull/39):
   builds ziggurat-server from the tube0013 fork's `tcp-adaptor-support` branch
   instead of the crates.io release.
 - Adds a `network_address` option to connect to the RCP over a raw TCP socket
   (`tcp://host:port`) instead of a local serial device.

## 0.2.0

 - Bump ziggurat to the 0.1.0 release on crates.io.
 - Routing and path cost fixes, in addition to frame parser hardening.

## 0.1.1

 - Bump ziggurat to 6e0df44f.
 - Adds energy + network scanning APIs and the ability to permit joins on just a single router.

## 0.1.0

- Initial release
