# Home Assistant App: Ziggurat

Ziggurat is an experimental Zigbee stack written in Rust. In contrast to microcontroller
based Zigbee stacks like EmberZNet and Z-Stack, Ziggurat runs entirely on your computer
and relies on the radio portion for simple packet sending and receiving. This removes
practically all resource limitations from your Zigbee network, completely open sources
the entire Zigbee stack end-to-end, and allows for complex bugs to be fixed more easily.

See the main [ziggurat](https://github.com/zigpy/ziggurat) project for more information.

ZHA connects to the add-on through the [zigpy-ziggurat](https://github.com/zigpy/zigpy-ziggurat) radio library.
