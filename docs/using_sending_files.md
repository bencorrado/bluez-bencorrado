---
title: "Sending files"
table_of_contents: True
---

# Using Bluetooth to send files on Ubuntu Core

This section describes what has to be done to be able to send files over
Bluetooth using Ubuntu Core device. It will focus on the OBEX Object Push
profile which is a standard Bluetooth profile for such an use case.

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

