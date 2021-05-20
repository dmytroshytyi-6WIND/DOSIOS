## Welcome to dosiOS Page - Edge Computin Platform with free access.

Create your service functions, chain them with virtual switches and physical ports of equipment. Accelerate packet exchange with DPDK.

Use NETCONF protocol and YANG modelisation language for dosiOS management. Perform identification and physical port mapping on initial setup with provided toolkits.

Set the parameters of service function deployment (cpu, ram, ifs, disks) and virtual switch configuration (trunk, access, tag, stacked-vlans).


Available release is ALPHA version available for comments/issues/improvements.

To modify value in dosiOS (epc part of the tree) you need first to delete current value and after set new value. Modification of values not guaranteed and can lead to errors in current stage.

### Installation
 
Repository provides iso image that should be flashed on the usb drive.
 
```
1. install qemu
2. qemu-img convert dosiOS-2102.iso -O raw dosiOS-2102.img
3. dd if=dosiOS-2102.img of=/dev/sdX
```

After booting from usb drive you will be in "cloud image" of dosiOS.

```
Login: tmpuser
Passwd: tmppwd
```

Confirm that your flash drive is something like /dev/sda and your hard drive is something like /dev/sdb.

Configuration should be performed in the BIOS (HDD priorities)

dosiOS will be installed on the /dev/sdb by default.

You may install dosiOS on the hard drive with the next command:

```dosiOS installation on hard drive
tmpuser@node:$ install image
```

Script of installation will be triggered:

```dosiOS installation scenario
select conf file "/config/config.boot"
select console ttyS0
select the console speed 115200
Disk label: gpt
```

### dosiOS configuration

ecp == Edge Computing Platform

### Login/Password for installed system:

System:

```credentials system
Login: dosiOS-2102
Password: dosiOS-2102
```

Grub:

```credentials grub
Login: dosiOS-2102
Password: dosiOS-2102
```

#### Port identification and mapping

This command helps to identify the connected NICs. Node address + kernel driver (igb)

```port list
ecp list-nic-pci
```

To identify phy port of the NIC use this command (the phy port will be blinking):

```port ident
ecp blink-port 0000:0a:00.0 igb // igb kernel driver for 1G NICs (check ecp list-nic-pci)
```

Next step to map ports to addresses:

```port mapping
configure
set ecp interfaces vfpX pci-addr 0000:0a.00.0
```

#### 2 types of interfaces

The interfaces below represent physical ports of the equipment 
```port mapping
/ecp/intefaces
```

The interfaces below represent virtual mgmnt ports connected with representation of physical ports (on the same link) 
They apper when representation of physical ports is configured on the switch. 

```port mapping
/interfaces/dataplane/dp0pX
```

#### Service function configuration

```sf config
config
set ecp vms sf1 boot-from hd
set ecp vms sf1 cpu 5
set ecp vms sf1 ram 4
set ecp vms sf1 devices 1 drive drive-type disk
set ecp vms sf1 devices 1 drive file 'http://10.0.0.2/vRouter.qcow2'
```

#### vswitch configuration

Add physical port to switch. When the physical port added to switch, you will find new interface dp0pX in the /root/interfaces/dataplane. 
This dp0pX is configured for management. Confirm that it was configured by 

```add phy port and service function to switch
set ecp switches sw1 ports 1 interface vfp1
set ecp switches sw1 ports 2 vm sf1
set ecp switches sw1 ports 2 vm-port 1
```
