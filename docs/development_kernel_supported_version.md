---
title: "Linux kernel supported versions"
table_of_contents: True
---

# Linux kernel supported versions

Given that the *bluez* snap currently packages BlueZ v5.37 the ideall kernel
version a new IoT system should target is Linux kernel v4.5. Keep in mind,
however, that the bits that depend on this kernel version are related to logging
therefore it will be perfectly safe to pick v4.4 which is a long term supported
version of a kernel. In fact you could go back in time to the version v3.10 and
still be pretty happy with the choice however it will require you to backport
the Bluetooth part of the kernel from the newer release. This can be easilly
done using [Kernel Backporst
Project](https://backports.wiki.kernel.org/index.php/Main_Page)

In short:

| Kernel version | Comment                                                   |
|:--------------:|-----------------------------------------------------------|
| >= 4.5         | Will work out of the box                                  |
| =  4.4         | Will also work but advanced debug logging will be limited |
| >= 3.10        | Ok if the Bluetooth parts ar backported                   |

