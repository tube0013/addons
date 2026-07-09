# Changelog

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
