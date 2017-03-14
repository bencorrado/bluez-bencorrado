---
title: "Prerequisites"
table_of_contents: True
---

# Prerequisites

This section describes the requirements that must be satisfied in order to use
Bluetooth on an Ubuntu Core device.

In short you have to make sure that:

 * The snap is installed
 * The Bluetooth daemons are running
 * The plugs and slots are connected

## The bluez snap is installed

Make sure that the latest *bluez* snap is installed. You can check this by using
*snap list* command:

```
kzapalowicz@core16:~$ snap list
Name                            Version        Rev   Developer  Notes
bluez                           5.37-2         27    canonical  -
canonical-se-engineering-tests  6              113   canonical  -
core                            16-2           1431  canonical  -
pi2-kernel                      4.4.0-1040-47  26    canonical  -
pi3                             16.04-0.5      14    canonical  -
udisks2                         2.1.7-6        45    canonical  -
wireless-tools                  1              5     canonical  -
kzapalowicz@core16:~$
```

If *bluez* is not listed by the above command you can install it with:

```
kzapalowicz@core16:~$ sudo snap install bluez
```

## Service is up and running

Normally, once the snap is installed, the Bluetooth service is up and running.
Nevertheless it is still good to verify this. The expected output should contain
both *bluetoothd* and *obexd*.

```
kzapalowicz@core16:~$ ps aux | grep bluetooth | awk '{print $1" "$11}'
root /snap/bluez/27/usr/lib/bluetooth/bluetoothd
root /snap/bluez/27/usr/lib/bluetooth/obexd
kzapalo+ grep
kzapalowicz@core16:~$
```

Note that the *awk* part in the above command is not needed, it is just to make
the output look pretty.

Note that you need *bluetoothd* for the regular Bluetooth usage, however it is
not enough for exchanging files over Bluetooth. For this to work you need the
*obexd* daemon. It is mentioned here because, for example, on Ubuntu Desktop the
*obexd* is not started by default.

## The plugs and slots are connected

Checking for the Bluetooth plug and slot being auto-connected is one of the snap
verification criteria therefore in 99.9% cases it will be as expected. For the
sake of exercise it is good to verify


```
kzapalowicz@core16:~$ snap interfaces | grep bluez
bluez:service             bluez:client
-                         bluez:bluetooth-control
kzapalowicz@core16:~$
```

You should expect the output like the above, that is the *bluez:service* slot is
connected with the *bluez:client* plug.

