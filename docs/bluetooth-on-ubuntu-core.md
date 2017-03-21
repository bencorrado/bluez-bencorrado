---
title: "Bluetooth on Ubuntu Core"
table_of_contents: True
---

# Bluetooth on Ubuntu Core

Bluetooth on Ubuntu Core is provided by the BlueZ stack which is an official
Linux Bluetooth protocol stack. The lower-level part of it comes with the kernel
snap while the user-space portion can be installed as a separate snap.

## The Bluetooth interfaces on Ubuntu Core

On Ubuntu Core each application can access and interact with the Bluetooth
service only through two specialized interfaces called:

 * bluetooth-control
 * bluez

You can learn more on the [interfaces
documentation](https://docs.ubuntu.com/core/en/reference/interfaces/index)

Note that unlike the *bluetooth-control* interface the *bluez* interface is not
installed by the *core* snap, neither the *gadget* nor *kernel*. It shall be
installed by the application snap.

### Interface bluetooth-control

The common naming scheme for interfaces on Ubuntu Core states that all
interfaces that allow managing a resource should end with *-control* post-fix.

The *bluetooth-control* interface follows this pattern. It allows managing the
kernel side of the Bluetooth stack. In particular, it offers direct access to
the Bluetooth related nodes under */sys/devices* and */sys/class* directories
as well as accessing */dev/vhci*.

Normally an application should not need to use this interface unless it would
like to talk directly to the underlying hardware.

### Interface bluez

The *bluez* interface defines the slot and plug sides for a user-space
application that would like to use Bluetooth for communication. BlueZ itself
uses D-Bus as a transport therefore this interface grants access to the
*org.bluez* D-Bus interface as well as some necessary bits from
*org.freedesktop*

### Putting it all together

As you know now there are two Bluetooth related interfaces on Ubuntu Core, the:
*bluetooth-control* and *bluez*. One of them is provided by the *core* snap
while the another is provided by the application snap.

On a system without *bluez* snap installed, type:

```
$ snap interfaces | grep blue
:bluetooth-control        -
```

Observe that there is a *bluetooth-control* slot provided by the core snap. This
is unlike to *bluez* because no snap exists which provides a slot based on the
*bluez* interface. You need an application snap to reveal it.
