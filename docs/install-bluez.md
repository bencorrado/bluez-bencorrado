---
title: "Install bluez snap"
table_of_contents: True
---

# Snap installation

The *bluez* snap can be installed as any other snap on Ubuntu Core, type:

```
kzapalowicz@core16:~$ snap install bluez
```

Now you should see that the snap is being downloaded and installed. Finally you
will be informed that the snap has been installed and your screen should look
like:

```
$ snap install bluez
bluez 5.37-2 from 'canonical' installed
```

The naming scheme for *bluez* snap includes the current BlueZ version being
packaged in the snap (5.37 in this case) and the revision of the snap itself
(2nd in this case). Whenever the snap is updated but still provides BlueZ
version 5.37 the last digit will be incremented.

The above output informs the the BlueZ 5.37 has been installed on the Ubuntu
Core. This effect is analogical to typing

```
$ sudo apt-get install bluez
```

on a classic Ubuntu flavor that you run on your desktop or laptop computer.

Now, lets do the interfaces listing exercise from the previous section once
again. Type:


```
$ snap interfaces | grep blue
bluez:service             bluez:client
:bluetooth-control        -
kzapalowicz@core16:~$
```

This time it lists the bluez:service slot and the bluez:client plug that are
provided by the *bluez* snap itself.
