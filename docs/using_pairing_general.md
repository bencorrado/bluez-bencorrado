---
title: "General information on pairing"
table_of_contents: True
---

# Pairing

This section teaches how to pair two Bluetooth devices using *bluetoothctl* -
the command-line interface to BlueZ.

## What is pairing

In Bluetooth terminology the pairing is the process of making two devices know
each other. It boils down to exchanging so called *link-keys* that are used to
secure the communication. The pairing process involves authentication however
due to the nature and variety of Bluetooth devices there will be different ways
of confirming the pairing attempt.

* Devices such as keyboards or car-kits will require authentication by PIN/passkey code
* Other devices will provide yes/no choice to the pairing attempt
* Devices without an input interface such as headsets or speakers will not
  require the user to confirm the pairing attempt at all.

Pairing with a remote device can be done in two ways due to the fact that it can
be initiated from both endpoints. Both ways are described further in this
section.

* [Inbound pairing](stacks/bluetooth/using_pairing_from_remote_device.html)
* [Outbound pairing](stacks/bluetooth/using_pairing_from_ubuntu_core.html)

## Handling authentication requests by BlueZ

The pairing procedure includes an authentication that requires confirmation by
the user. If you have ever used Bluetooth previously you probably remeber
entering pin code or answering "would you like to connect yes/no" question.

The authentication requests are handled by an entity called *agent*. It
implements the [D-Bus agent
api](https://git.kernel.org/pub/scm/bluetooth/bluez.git/tree/doc/agent-api.txt)
and it's purpose is to prompt the user for confirmation whenever it is
necessary.

The agent has to be registered explicitly by typing:

```
$ sudo bluetoothctl
[bluetooth]# agent on
Agent registered
```

The pairing section will walk you through pairing procedure with keyboard. It
will require pin code authentication.

