---
title: "Linux kernel configuration options"
table_of_contents: True
---

# Kernel configuration options

This section lists the Linux kernel configuration options related to the
Bluetooth support. It is based on the Linux kernel v4.4.

Most of the Bluetooth systems will support the *Classic* mode therefore you need
to make sure that the following options are selected.

```
  [*] Networking support
      <M> Bluetooth subsystem support
    	[*]   Bluetooth Classic (BR/EDR) features
	<M>     RFCOMM protocol support
	[*]       RFCOMM TTY support
	<M>     BNEP protocol support
	[*]       Multicast filter
	[*]       Protocol filter support
	<M>     CMTP protocol support
	<M>     HIDP protocol support
	[*]     Bluetooth High Speed (HS) features
	[*]   Bluetooth Low Energy (LE) features
	<M>     Bluetooth 6LoWPAN support
	[ ]   Bluetooth self testing support
	[*]   Export Bluetooth internals in debugf
```

Note that this is a general set and it might be further tweaked to match your
device use-cases and capabilities. For example on devices that will not offer
networking over Bluetooth the *BNEP* can be disabled. 

Note that if your Bluetooth controller (chip) supports Bluetooth Low Energy
leave the BLE related options selected, disable otherwise:

```
  [*] Networking support
      <M> Bluetooth subsystem support
	[*]   Bluetooth Low Energy (LE) features
	<M>     Bluetooth 6LoWPAN support
```

It is also important to remember about the [UHID
driver](https://lwn.net/Articles/508083/). It is needed for
Bluetooth Low Energy (a.k.a. Smart) keyboards and mice.

```
Device Drivers
  HID support
    {M} HID bus support
    [*]   Battery level reporting for HID devices
    [*]   /dev/hidraw raw HID device support
    <M>   User-space I/O driver support for HID subsystem
    <M>   Generic HID driver 
```

