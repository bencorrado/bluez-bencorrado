---
title: "Bluetooth on Ubuntu Core"
table_of_contents: True
---

# Bluetooth on Ubuntu Core

Bluetooth on Ubuntu Core is provided by the BlueZ stack which is an official
Linux Bluetooth protocol stack. The lower-level part of it comes with the kernel
snap while the user-space portion can be installed separately as a snap.

## The Bluetooth interfaces on Ubuntu Core

On Ubuntu Core the bridge between the snaps and the Bluetooth stack are the
Bluetooth interfaces called:

 - bluetooth-control
 - bluez

Note that unlike the *bluetooth-control* interface the *bluez* interface is not
installed by the *core* snap, neither the *gadget* nor *kernel*. It shall be
installed by the application snap.

### Interface bluetooth-control

The common naming scheme for interfaces on Ubuntu Core states that all
interfaces that allow managing a resource should end with *-control* post-fix.

The *bluetooth-control* interface follows this pattern. It allows managing the
kernel side of the Bluetooth stack. In particular it offers a direct access to
the *bluetooth* under */sys/devices* and */sys/class* directories as well as
accessing */dev/vhci*.

Normally an application should not need to use it unless it would like to talk
directly to the underlying hardware.

### Interface bluez

The *bluez* interface defines the slot and plug sides for a user-space
application that would like to use Bluetooth for communication. The BlueZ itself
uses the D-Bus as a transport therefore this interface unlocks access to the
*org.bluez* D-Bus interface as well as some necessary bits from
*org.freedesktop*

### Putting it all together

In the next section you will install the *bluez* snap however before it happens
let's take a look on available interfaces. As you know now there are two
Bluetooth related interfaces on Ubuntu Core, the: *bluetooth-control* and
*bluez*. One of them is provided by the *core* snap while the another is
provided by the application snap.

On a system without *bluez* snap installed, type:

```
kzapalowicz@core16:~$ snap interfaces | grep blue
:bluetooth-control        -
```

You will see for yourself that obviously there is a core-installed
*bluetooth-control* interface available but there is no *bluez* at all. It is
defined by *snapd* however you need an application snap to unlock it.
