Overview
========

This document gives a short overview of what is possible with the
bluetooth snap at the moment.

It is based on the BlueZ bluetooth stack and we're based on the 5.37
release. For more details about BlueZ itself see its homepage at
http://www.bluez.org

To easily interact with the BlueZ service the snap provides a small
utility called bluetoothctl which can be started from the command
line. The use in different scenarios will be explained in the
following sections.

NOTE: The bluetoothctl utility used on the examples below just uses
the DBus APIs provided by the BlueZ service. It does nothing else.
The DBus APIs of the BlueZ service are available at

 https://git.kernel.org/cgit/bluetooth/bluez.git/tree/doc

There are also various small python script examples to play with the
DBus API at

 https://git.kernel.org/cgit/bluetooth/bluez.git/tree/test

# Power management and device discovery

1. Start up bluetoothctl

```
$ bluetoothctl
[NEW] Controller 1C:C3:E4:79:43:4C localhost.localdomain [default]
[bluetooth]#
```

2. Power on the found blutooth controller

```
[bluetooth]# power on
[CHG] Controller 5C:C5:D4:39:40:4E Powered: yes
```

3. Start scanning for other Bluetooth devices

```
[bluetooth]# scan on
Discovery started
[CHG] Controller 5C:C5:D4:39:40:4E Discovering: yes
[NEW] Device 5C:31:3E:71:0C:E7 5C-31-3E-71-0C-E7
```
Here we've found one device. It's MAC address is revevant in the next
steps.

4. We can now show various information about the device we've found

```
[bluetooth]# info 5C:31:3E:71:0C:E7
Device 5C:31:3E:71:0C:E7
    Alias: 5C-31-3E-71-0C-E7
    Paired: no
    Trusted: no
    Blocked: no
    Connected: no
    LegacyPairing: no
    UUID: Vendor specific           (0d27ffff-f0d4-469d-afd3-605a6ebbdb13)
    ManufacturerData Key: 0xd0d1
    ManufacturerData Value: 0x01
    ManufacturerData Value: 0x00
    ManufacturerData Value: 0x00
    ManufacturerData Value: 0x00
    ManufacturerData Value: 0x08
    ManufacturerData Value: 0x00
    RSSI: -52
```

NOTE: This doesn't tell us which type of device (BR/EDR or LE) we've
found. All devices are considered equal in BlueZ. However the available
information and profiles will differ. In the example above we have a LE
device with a vendor specific profile. Also as we never connected yet
we don't know fully which profiles the device provide.

# Pairing with other devices

To pair with other devices BlueZ uses an agent-style dbus API. See the
following links for more details on this:

 * https://git.kernel.org/cgit/bluetooth/bluez.git/tree/doc/agent-api.txt
 * https://git.kernel.org/cgit/bluetooth/bluez.git/tree/doc/device-api.txt

Within the bluetoothctl utility we can register such an agent with a
specific IO capability with the BlueZ service and then process any
further pairing operation.

If no agent is registered with BlueZ and a pairing operation happens
BlueZ will consider the IO capability as NoInputNoOutput and try to
pair with the remove device without further user interaction.

NOTE: If using NoInputNoOutput this results in an unauthenticated
communication link with the remote device and will always use the
JustWorks method.

In the following example we will register an agent with the IO
capability DisplayYesNo.

1. Make sure the device you want to pair with is available and
   turned on. You can with the following command if the snappy
   devices has found that device:

```
[bluetooth]# devices
Device AC:73:13:74:03:E6 Aquaris E4.5
```

2. Register our agent with the BlueZ service (we're using the
   DisplayYesNo capability here but you can use any other
   capability too)

```
[bluetooth]# agent DisplayYesNo
Agent registered
```

3. Initiate the pairing process with the remote device

```
[bluetooth]# pair AC:73:13:74:03:E6
Attempting to pair with AC:73:13:74:03:E6
```

In the simplest case both devices choose the JustWorks pairing method
and no further user interaction is required. If further user
interaction is need the bluetootctl utility will prompt you for that.

For the JustWorks case the further output looks like

```
[CHG] Device AC:73:13:74:03:E6 Connected: yes
[CHG] Device AC:73:13:74:03:E6 UUIDs: 00001104-0000-1000-8000-00805f9b34fb
[CHG] Device AC:73:13:74:03:E6 UUIDs: 00001105-0000-1000-8000-00805f9b34fb
[CHG] Device AC:73:13:74:03:E6 UUIDs: 00001106-0000-1000-8000-00805f9b34fb
[CHG] Device AC:73:13:74:03:E6 UUIDs: 0000110a-0000-1000-8000-00805f9b34fb
[CHG] Device AC:73:13:74:03:E6 UUIDs: 0000110b-0000-1000-8000-00805f9b34fb
[CHG] Device AC:73:13:74:03:E6 UUIDs: 0000110c-0000-1000-8000-00805f9b34fb
[CHG] Device AC:73:13:74:03:E6 UUIDs: 0000110e-0000-1000-8000-00805f9b34fb
[CHG] Device AC:73:13:74:03:E6 UUIDs: 0000111e-0000-1000-8000-00805f9b34fb
[CHG] Device AC:73:13:74:03:E6 UUIDs: 0000112f-0000-1000-8000-00805f9b34fb
[CHG] Device AC:73:13:74:03:E6 UUIDs: 00001132-0000-1000-8000-00805f9b34fb
[CHG] Device AC:73:13:74:03:E6 UUIDs: 00001133-0000-1000-8000-00805f9b34fb
[CHG] Device AC:73:13:74:03:E6 UUIDs: 00001200-0000-1000-8000-00805f9b34fb
[CHG] Device AC:73:13:74:03:E6 UUIDs: 00001800-0000-1000-8000-00805f9b34fb
[CHG] Device AC:73:13:74:03:E6 UUIDs: 00001801-0000-1000-8000-00805f9b34fb
[CHG] Device AC:73:13:74:03:E6 UUIDs: 00005005-0000-1000-8000-0002ee000001
[CHG] Device AC:73:13:74:03:E6 Paired: yes
Pairing successful
[CHG] Device AC:73:13:74:03:E6 Connected: no
[CHG] Device AC:73:13:74:03:E6 RSSI: -65
```

The device is now marked as paired. The switch from connected to not
connected happens cause BlueZ creates a L2CAP connection to perform
further SDP requests to find out about all the profiles the remote
device supports. It will not try to automatically connect any of the
profiles. However it is possible that the remote device starts to
initiate a connection.

4. As we're now paired with the remote device we can look at its
   provided profiles again which is now extended.

```
[bluetooth]# info AC:73:13:74:03:E6
Device AC:73:13:74:03:E6
    Name: Aquaris E4.5
    Alias: Aquaris E4.5
    Class: 0x3c0000
    Paired: yes
    Trusted: no
    Blockcked: no
    Connected: no
    LegacyPairing: no
    UUID: IrMC Sync                          (00001104-0000-1000-8000-00805f9b34fb)
    UUID: OBEX Object Push            (00001105-0000-1000-8000-00805f9b34fb)
    UUID: OBEX File Transfer           (00001106-0000-1000-8000-00805f9b34fb)
    UUID: Audio Source                  (0000110a-0000-1000-8000-00805f9b34fb)
    UUID: Audio Sink                 (0000110b-0000-1000-8000-00805f9b34fb)
    UUID: A/V Remote Controllerol Target (0000110c-0000-1000-8000-00805f9b34fb)
    UUID: A/V Remote Controllerolrol        (0000110e-0000-1000-8000-00805f9b34fb)
    UUID: Handsfree                     (0000111e-0000-1000-8000-00805f9b34fb)
    UUID: Phonebook Acceptess Server   (0000112f-0000-1000-8000-00805f9b34fb)
    UUID: Message Acceptessss Server     (00001132-0000-1000-8000-00805f9b34fb)
    UUID: Message Notifyification Se.. (00001133-0000-1000-8000-00805f9b34fb)
    UUID: PnP Information           (00001200-0000-1000-8000-00805f9b34fb)
    UUID: Generic Acceptessssccess Profile    (00001800-0000-1000-8000-00805f9b34fb)
    UUID: Generic   Attribute Profile (00001801-0000-1000-8000-00805f9b34fb)
    UUID: Vendor    specific           (00005005-0000-1000-8000-0002ee000001)
    Modalias: userb:v1D6Bp0246d0522
    RSSI: -65
    TxPower: 24

# Connect with HID Keyboard

NOTE - this has only been tested on a RiPi2.

In this example we want to connect a traditional BT keyboard.  To do
so follow the instructions from the previous example for pairing.

Once the keyboard has been successfully paired:

1. Setup the keyboard as trusted

```
[bluetooth]# connect 5C:31:3E:71:0C:E7
Attempting to connect to 5C:31:3E:71:0C:E7
[CHG] Device 5C:31:3E:71:0C:E7 Connected: yes
Connection successful
```

At this point, you should be able to use the BT device for console input.

# Connect with LE devices

In this example we want to connect with a LE device and explore its
provided GATT services. The uses LE device in this example provides
a number of vendor specific GATT services but also the standard
battery GATT service.

1. Connect with the discovered LE device

```
[bluetooth]# connect 5C:31:3E:71:0C:E7
Attempting to connect to 5C:31:3E:71:0C:E7
[CHG] Device 5C:31:3E:71:0C:E7 Connected: yes
Connection successful
```

2. As soon as BlueZ has discovered which GATT services are available
   the bluetoothctl utility will print out these

```
[CHG] Device 5C:31:3E:71:0C:E7 UUIDs: 00001800-0000-1000-8000-00805f9b34fb
[CHG] Device 5C:31:3E:71:0C:E7 UUIDs: 00001801-0000-1000-8000-00805f9b34fb
[CHG] Device 5C:31:3E:71:0C:E7 UUIDs: 0000180f-0000-1000-8000-00805f9b34fb
[CHG] Device 5C:31:3E:71:0C:E7 UUIDs: 0d271100-f0d4-469d-afd3-605a6ebbdb13
[CHG] Device 5C:31:3E:71:0C:E7 UUIDs: 0d27fa01-f0d4-469d-afd3-605a6ebbdb13
[CHG] Device 5C:31:3E:71:0C:E7 UUIDs: 0d27fa60-f0d4-469d-afd3-605a6ebbdb13
[CHG] Device 5C:31:3E:71:0C:E7 UUIDs: 0d27ffc0-f0d4-469d-afd3-605a6ebbdb13
[NEW] Service /org/bluez/hci0/dev_5C_31_3E_71_0C_E7/service0010 Vendor specific (Primary)
[NEW] Characteristic /org/bluez/hci0/dev_5C_31_3E_71_0C_E7/service0010/char0011 Vendor specific
[NEW] Characteristic /org/bluez/hci0/dev_5C_31_3E_71_0C_E7/service0010/char0013 Vendor specific
[NEW] Characteristic /org/bluez/hci0/dev_5C_31_3E_71_0C_E7/service0010/char0015 Vendor specific
[NEW] Characteristic /org/bluez/hci0/dev_5C_31_3E_71_0C_E7/service0010/char0017 Vendor specific
[NEW] Characteristic /org/bluez/hci0/dev_5C_31_3E_71_0C_E7/service0010/char0019 Vendor specific
[NEW] Characteristic /org/bluez/hci0/dev_5C_31_3E_71_0C_E7/service0010/char001b Vendor specific
[NEW] Characteristic /org/bluez/hci0/dev_5C_31_3E_71_0C_E7/service0010/char001d Vendor specific
[NEW] Service /org/bluez/hci0/dev_5C_31_3E_71_0C_E7/service001f Vendor specific (Primary)
[NEW] Characteristic /org/bluez/hci0/dev_5C_31_3E_71_0C_E7/service001f/char0020 Vendor specific
[NEW] Characteristic /org/bluez/hci0/dev_5C_31_3E_71_0C_E7/service001f/char0022 Vendor specific
[NEW] Characteristic /org/bluez/hci0/dev_5C_31_3E_71_0C_E7/service001f/char0024 Vendor specific
[NEW] Characteristic /org/bluez/hci0/dev_5C_31_3E_71_0C_E7/service001f/char0026 Vendor specific
[NEW] Descriptor /org/bluez/hci0/dev_5C_31_3E_71_0C_E7/service001f/char0026/desc0028 Client Characteristic Configuration
[NEW] Service /org/bluez/hci0/dev_5C_31_3E_71_0C_E7/service0029 Battery Service (Primary)
[NEW] Characteristic /org/bluez/hci0/dev_5C_31_3E_71_0C_E7/service0029/char002a Battery Level
[NEW] Descriptor /org/bluez/hci0/dev_5C_31_3E_71_0C_E7/service0029/char002a/desc002c Client Characteristic Configuration
[NEW] Service /org/bluez/hci0/dev_5C_31_3E_71_0C_E7/service002d Vendor specific (Primary)
[NEW] Characteristic /org/bluez/hci0/dev_5C_31_3E_71_0C_E7/service002d/char002e Vendor specific
[NEW] Descriptor /org/bluez/hci0/dev_5C_31_3E_71_0C_E7/service002d/char002e/desc0030 Client Characteristic Configuration
[NEW] Descriptor /org/bluez/hci0/dev_5C_31_3E_71_0C_E7/service002d/char002e/desc0031 Characteristic User Description
[NEW] Service /org/bluez/hci0/dev_5C_31_3E_71_0C_E7/service0032 Vendor specific (Primary)
[NEW] Characteristic /org/bluez/hci0/dev_5C_31_3E_71_0C_E7/service0032/char0033 Vendor specific
[NEW] Descriptor /org/bluez/hci0/dev_5C_31_3E_71_0C_E7/service0032/char0033/desc0035 Client Characteristic Configuration
[NEW] Descriptor /org/bluez/hci0/dev_5C_31_3E_71_0C_E7/service0032/char0033/desc0036 Characteristic User Description
[NEW] Characteristic /org/bluez/hci0/dev_5C_31_3E_71_0C_E7/service0032/char0037 Vendor specific
[NEW] Descriptor /org/bluez/hci0/dev_5C_31_3E_71_0C_E7/service0032/char0037/desc0039 Client Characteristic Configuration
[NEW] Descriptor /org/bluez/hci0/dev_5C_31_3E_71_0C_E7/service0032/char0037/desc003a Characteristic User Description
[NEW] Characteristic /org/bluez/hci0/dev_5C_31_3E_71_0C_E7/service0032/char003b Vendor specific
[NEW] Descriptor /org/bluez/hci0/dev_5C_31_3E_71_0C_E7/service0032/char003b/desc003d Client Characteristic Configuration
[NEW] Descriptor /org/bluez/hci0/dev_5C_31_3E_71_0C_E7/service0032/char003b/desc003e Characteristic User Description
[CHG] Device 5C:31:3E:71:0C:E7 GattServices: /org/bluez/hci0/dev_5C_31_3E_71_0C_E7/service0010
[CHG] Device 5C:31:3E:71:0C:E7 GattServices: /org/bluez/hci0/dev_5C_31_3E_71_0C_E7/service001f
[CHG] Device 5C:31:3E:71:0C:E7 GattServices: /org/bluez/hci0/dev_5C_31_3E_71_0C_E7/service0029
[CHG] Device 5C:31:3E:71:0C:E7 GattServices: /org/bluez/hci0/dev_5C_31_3E_71_0C_E7/service002d
[CHG] Device 5C:31:3E:71:0C:E7 GattServices: /org/bluez/hci0/dev_5C_31_3E_71_0C_E7/service0032
[CHG] Device 5C:31:3E:71:0C:E7 Name: X4-LIFE Xmarty 2.0
[CHG] Device 5C:31:3E:71:0C:E7 Alias: X4-LIFE Xmarty 2.0
```

3. We can now select specific characteristics and read their values
   or if possible also write them. In this example we select the
   battery level characteristic from the battery GATT service.

```
[5C-31-3E-71-0C-E7]# select-attribute /org/bluez/hci0/dev_5C_31_3E_71_0C_E7/service0029/char002a
[X4-LIFE Xmarty 2.0:/service0029/char002a]# read
Attempting to read /org/bluez/hci0/dev_5C_31_3E_71_0C_E7/service0029/char002a
[CHG] Attribute /org/bluez/hci0/dev_5C_31_3E_71_0C_E7/service0029/char002a Value: 0x49
  49
```

4. We can also register us to receive notifications when the value
   of a characteristic changes

```
[X4-LIFE Xmarty 2.0:/service0029/char002a]# notify on
[CHG] Attribute /org/bluez/hci0/dev_5C_31_3E_71_0C_E7/service0029/char002a Notifying: yes
Notify started
```
This will now notify us whenever the value of the selected
characteristic changes.
