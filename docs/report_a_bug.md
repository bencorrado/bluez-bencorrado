---
title: "Report a Bug"
table_of_contents: False
---

# Report a Bug

Bugs can be reported [here](https://bugs.launchpad.net/snappy-hwe-snaps/+filebug).

## Information required to include in a bug report

When submitting a bug report, please attach:

 * */var/log/syslog*
 * Bluetooth adapter information
 * List of paired devices
 * State of the Bluetooth kill-switch
 * The HCI trace

### Bluetooth adapter information

```
$ sudo hciconfig -a
```

### List of paired devices

```
$ bluetoothctl
[bluetooth]# show
[bluetooth]# devices
[bluetooth]# info <mac addr of any device you have problems with>
```

### State of the Bluetooth kill-switch

```
rfkill list
```

Note that the *rfkill* command is a part of *wireless-tools* snap.

### The HCI trace

The HCI trace is a snapshot of the communication between the Bluetooth host
(software stack) and the Bluetooth controller (the chip). The *btmon* tool can
be used to capture such for both live debugging and saving it for later. Note
that the HCI trace needs to be captured at the same time the Bluetooth issue
occurs.

The *btmon* executed without any parameters will offer live debugging which
will print in a ‘tail -f’ fashion an ongoing exchange of the commands and
events between the stack and the chip. It is possible however to make it save
the data in the [snoop format](https://tools.ietf.org/html/rfc1761) which can
later be viewed using for example [Wireshark](https://www.wireshark.org).

Now, for live debugging:

```
$ sudo bluez.btmon
```

For saving it later in a .snoop format:

```
$ sudo bluez.btmon --write ~/hcitrace.snoop
```

The nice thing about btmon and how it works is that it is possible to have
several versions of it executed simultaneously. This allows capturing logs as
in the last example in one shell and viewing it live in another.
