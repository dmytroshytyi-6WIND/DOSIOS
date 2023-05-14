---
sort: 1
---

# Installation

Repository will provide iso image that should be flashed on the usb drive.
 
```
1. install qemu
2. qemu-img convert dosios2102.iso -O raw dosiOS-2102.img
3. dd if=dosiOS-2102.img of=/dev/sdX
```

After booting from usb drive you will be in "cloud image" of DOSIOS.

```
Login: tmpuser
Passwd: tmppwd
```

Confirm that your flash drive is something like /dev/sda and your hard drive is something like /dev/sdb.

Configuration should be performed in the BIOS (HDD priorities)

DOSIOS will be installed on the /dev/sdb by default.

You may install DOSIiOS on the hard drive with the next command:

```
tmpuser@node:$ install image
```

Script of installation will be triggered:

```
select conf file "/config/config.boot"
select console ttyS0
select the console speed 115200
Disk label: gpt
```