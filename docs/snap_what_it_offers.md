---
title: "What the bluez snap offers"
table_of_contents: True
---

# What the bluez snap offers

This section will describe what comes with the *bluez* snap.

## Commands

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

## Plugs and slots

The *bluez* snap provides two daemons for the 'Bluetooth' service to work. Each
one of them provides a *service* slot for the applications to be able to talk to
them.

