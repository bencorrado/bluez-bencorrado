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

## Snap installation

The *bluez* snap shall be installed as any other snap on Ubuntu Core, type:

```
kzapalowicz@core16:~$ snap install bluez
```

Now you should see that the snap is being downloaded and installed. Finally you
will be informed that the snap has been installed and your screen should look
like:

```
kzapalowicz@core16:~$ snap install bluez
bluez 5.37-2 from 'canonical' installed
```

The naming scheme for *bluez* snap includes the current BlueZ version being
packaged in the snap (5.37 in this case) and the revision of the snap itself
(2nd in this case). Whenever the snap is updated but still provides BlueZ
version 5.37 the last digit will be incremented.

The above output informs the the BlueZ 5.37 has been installed on the Ubuntu
Core. This effect is analogical to typing

```
$ sudo apt-get install bluez
```

on a classic Ubuntu flavor that you run on your desktop or laptop computer.

Now, lets do the interfaces listing exercise from the previous section once
again. Type:


```
kzapalowicz@core16:~$ snap interfaces | grep blue
bluez:service             bluez:client
:bluetooth-control        -
kzapalowicz@core16:~$
```

This time it lists the bluez:service slot and the bluez:client plug that are
provided by the *bluez* snap itself.

### Refreshing the snap

Right now you have the *bluez* snap installed from the *stable* channel.
Alternatively however you could refresh the snap from another channel namely to
get the access to not yet released features yet made available for testing.

Note that the official release process uses different channels as a sign of
maturity of a particular snap version. It starts in the *beta* and *edge* then
is moved through *candidate* to the *stable* channel.

Refresh the snap from the *edge* channel to get access to the version with the
most features, type:


```
kzapalowicz@core16:~$ snap refresh --edge bluez
```

You will see a very similar output to the one that you have seen
during the installation process:


```
kzapalowicz@core16:~$ snap refresh --edge bluez
bluez (edge) 5.37-2 from 'canonical' refreshed
```

## What the bluez snap offers

This section will describe what comes with the *bluez* snap.

### Commands

As first and foremost thing the *bluez* snap offers the BlueZ stack. Apart from
this it contains various tools shipped with the BlueZ itself. The following
table lists the commands that are provided by the *bluez* snap:

| Command      | Short description                                             |
|--------------|---------------------------------------------------------------|
| bluez        | The *bluetoothd* Bluetooth daemon                             |
| obex         | The *obexd* OBEX daemon                                       |
| bluetoothctl | A command-line interface to the BlueZ 			       |	
| obexctl      | A command-line interface to the BlueZ for file transfers      |
| hciconfig    | HCI device configuration utility                              |
| hcidump      | Reads raw HCI data and prints it on screen                    |
| hciattach    | Attach a serial UART to the BT stack as a transport interface |
| hcitool      | Tool used to configure Bluetooth connections                  |
| sdptool      | A tool to perform SDP queries on Bluetooth devices            |

Note that the amount of commands provided by this snap may change in the future
versions of it. For example the current version in *stable* channel provides
only *obexctl* and *bluetoothctl* commands.  There is however a quick way of
checking what a snap provides and to do this you can use the snap scheme for
exposing commands which is *snap_name.command*. So in order to learn all the
commands provided by the *bluez* snap type:


```
$ bluez.<TAB><TAB>
```

The double **TAB** indicates that you should hit the tab key twice for bash
auto-completion to kick in. Immediately you will be rewarded with the list
available commands:

```
kzapalowicz@core16:~$ bluez.
bluez.bluetoothctl  bluez.hciattach     bluez.hciconfig     bluez.hcidump
bluez.hcitool       bluez.obexctl       bluez.sdptool       
kzapalowicz@core16:~$ bluez.
```

Note that the daemons are not auto-completed. 

### Plugs and slots

The *bluez* snap provides two daemons for the 'Bluetooth' service to work. Each
one of them provides a *service* slot for the applications to be able to talk to
them.

