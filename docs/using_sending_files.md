---
title: "Sending files"
table_of_contents: True
---

# Using Bluetooth to send files on Ubuntu Core

This section describes what has to be done to be able to send files over
Bluetooth using Ubuntu Core device. It will focus on the OBEX Object Push
profile which is a standard Bluetooth profile for such an use case.

## Prerequisites

The bluez snap is installed

Service is up and running

The both devices shall be paired. Refer to the previous section in order to
learn how to do it.

The files will be sent to the server which is a Bluetooth-enabled device with
the Object Push profile enabled. If this is not fulfilled then Ubuntu Core will
fail to connect.

The minimal set of the Bluetooth profiles that must be available on the server
device is:

|   Profile        |  UUID  |
|------------------|:------:|
| GAP Service      | 0x1800 |
| GATT Server      | 0x1801 |
| OBEX Object Push | 0x1105 |

If you are unsure about which profiles are enabled, then check this using the
*sdptool* utility which is available on Ubuntu Desktop. Note that this tool is not
yet available on Ubuntu Core. This tool allows you to browse the service
database of a remote device thus making it possible to check the supported
profiles. You should look for output like below and verify that the OBEX Object
Push is listed.

```
% sdptool browse 00:1A:7D:DA:71:0F
Browsing 00:1A:7D:DA:71:0F ...
Service Name: GAP Service
Service RecHandle: 0x10001
Service Class ID List:
  "Generic Access" (0x1800)
Protocol Descriptor List:
  "L2CAP" (0x0100)
    PSM: 31
  "ATT" (0x0007)
    uint16: 0x0001
    uint16: 0x0007
Language Base Attr List:
  code_ISO639: 0x656e
  encoding:    0x6a
  base_offset: 0x100

Service Name: GATT Server
Service RecHandle: 0x10002
Service Class ID List:
  "Generic Attribute" (0x1801)
Protocol Descriptor List:
  "L2CAP" (0x0100)
    PSM: 31
  "ATT" (0x0007)
    uint16: 0x000c
    uint16: 0x000f
Language Base Attr List:
  code_ISO639: 0x656e
  encoding:    0x6a
  base_offset: 0x100

Failed to connect to SDP server on 00:1A:7D:DA:71:0F: Connection refused
Service Name: OBEX Object Push
Service RecHandle: 0x10004
Service Class ID List:
  "OBEX Object Push" (0x1105)
Protocol Descriptor List:
  "L2CAP" (0x0100)
  "RFCOMM" (0x0003)
    Channel: 1
  "OBEX" (0x0008)
Language Base Attr List:
  code_ISO639: 0x656e
  encoding:    0x6a
  base_offset: 0x100
Profile Descriptor List:
  "OBEX Object Push" (0x1105)
    Version: 0x0102
```

Note that the sdptool tool will be part of the bluez snap starting with version
5.37-3. It is expected to be released towards the end of March 2017.
o

## Connect OBEX Object Push profile

Once the pairing is successfully completed, it is time to connect the OBEX
Object Push profile. To interact with OBEX, the obexctl tool is used. Open
another terminal and type

```
$ sudo bluez.obexctl
```

You will see output like this:

```
$ sudo bluez.obexctl
[NEW] Client /org/bluez/obex 
[obex]#
```

This indicates that the OBEX client has been properly initialized and is
awaiting commands. The first thing to do is to get it connected to the remote
server which is the device that has been paired in the previous step. Type:

```
[obex]# connect 00:25:56:D1:36:6B
```

You will see output like:

```
Attempting to connect to 00:25:56:D1:36:6B
[NEW] Session /org/bluez/obex/client/session4 [default]
[NEW] ObjectPush /org/bluez/obex/client/session4 
Connection successful
[00:25:56:D1:36:6B]#
```

The above indicates that the connection has been established. Check the address
of the remote device in the prompt. It is now possible to send files.

## Sending files

In order to send files use the *send* command while connected to the OBEX Object
Push profile. For sending a file type:

```
[00:25:56:D1:36:6B]# send <path to file>
```

Note that the file you are about to send should be accessible to the snap,
therefore it must be placed in a readable location. For example:
/var/snap/bluez/current.

Below is example output of sending a file:

```
[00:25:56:D1:36:6B]# send /var/snap/bluez/current/f.txt
Attempting to send /var/snap/bluez/current/f.txt to /org/bluez/obex/client/session5
[NEW] Transfer /org/bluez/obex/client/session5/transfer10 
Transfer /org/bluez/obex/client/session5/transfer10
        Status: queued
        Name: f.txt
        Size: 4
        Filename: /var/snap/bluez/current/f.txt
        Session: /org/bluez/obex/client/session5
[CHG] Transfer /org/bluez/obex/client/session5/transfer10 Status: complete
[DEL] Transfer /org/bluez/obex/client/session5/transfer10
```

## Receiving files

By default there is no way to receive a file using Bluetooth on Ubuntu Core.
This is because the incoming transfer has to be allowed. However, the obexctl
tool does not provide such an agent. It is assumed that the application will
implement this. For reference, here is the OBEX D-Bus Agent API description:
https://git.kernel.org/cgit/bluetooth/bluez.git/tree/doc/obex-agent-api.txt

It might be possible to auto-allow all incoming transfers by passing the
--auto-allow flag to the obexd daemon. However, this is disabled because of
security reasons on Ubuntu Core.

For convenience, there will soon be a bluez-tests snap that packages the
simple-obex-agent Python script that implements the mentioned API. It can be
used to allow incoming file transfers through OBEX. The script itself is a
slightly modified version of
https://git.kernel.org/cgit/bluetooth/bluez.git/tree/test/simple-obex-agent.
The modifications make it compatible with Ubuntu Core specifics.  Usage
When the bluez-tests snap is available first install it, type:

```
$ sudo snap install bluez-tests
```

When the above operation successfully finishes, you are able to use it and
experiment with receiving file transfers. Open another shell on the device and
start the simple-obex-agent by typing:

```
$ sudo bluez-tests.simple-obex-agent
```

From now on it will listen for incoming OBEX transfers and when such transfers
happen, it will prompt for a decision: accept or deny.
















