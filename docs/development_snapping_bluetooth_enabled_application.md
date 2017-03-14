---
title: "Snapping Bluetooth-enabled application"
table_of_contents: True
---

# Snapping Bluetooth-enabled application

This section will show what are the necessary bits in the *snapcraft.yaml* while
snapping an application that uses Bluetooth.

## Bluetooth interfaces

There are two Bluetooth related interfaces available on Ubuntu Core.

**bluez** interface that allows accessing the Bluetooth service through D-Bus
API

**bluetooth-control** that can be used to manage the state of the Bluetooth
service as well as accessing the hardware directly bypassing the D-Bus API.

In normal circumstances the *bluez* interface shall be used as the uses cases
for *bluetooth-control* interface are somewhat special.

## Contents of the snapcraft.yaml

This section presents a *snapcraft.yaml* template for applications that use
Bluetooth.

The *bluez* snap discussed previously is a somewhat complex thing because it
includes both the Bluetooth service (*bluetoothd* and *obexd*) and the client
applications (*bluetoothctl* and *obexctl*). Because of this it defines the
slots and plugs.

The snatd-alone Bluetooth application that could be used in place of
*bluetoothctl* need to take care only of the client side. In fact all it has to
do is to define a *bluez* plug.

In the example below the *foo* application defines a *client* plug that is
nothing more than a plug side of the *bluez* interface. 

```
plugs:
  client:
      interface: bluez

apps:
  foo:
    command: "bin/foo-command"
    plugs: [client]

```

Note that if by any chance your application needs to talk directly to the chip
then the *bluetooth-control* interface will suit these needs better than the
*bluez*. 
