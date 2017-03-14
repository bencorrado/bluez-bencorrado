---
title: "General information on pairing"
table_of_contents: True
---

# Pairing

This section teaches how to pair two Bluetooth devices using *bluetoothctl* -
the command-line interface to BlueZ.

In Bluetooth terminology the pairing is the process of making two devices know
each other. It boils down to exchanging so called *link-keys* that are used to
secure the communication. The pairing process involves authentication however
due to the nature and variety of Bluetooth devices there will be different ways
of confirming the pairing attempt.

* Devices such as keyboards or car-kits will require authentication by pin code
* Other devices will provide yes/no choice to the pairing attempt
* Devices without an input interface such as headsets or speakers will not
  require the user to confirm the pairing attempt at all.

The pairing section will walk you through pairing procedure with keyboard. It
will require pin code authentication.

Pairing with a remote device can be done in two ways due to the fact that it can
be initiated from both endpoints. Both ways are described further in this
section.

