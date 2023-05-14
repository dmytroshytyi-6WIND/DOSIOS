---
sort: 2
---

# Configuration

ecp == Edge Computing Platform

## Login/Password for installed system:

System:

```
Login: dosios2102
Password: dosios2102
```

Grub:

```
Login: dosios2102
Password: dosios2102
```

## Hugetalbes configuration

By default Edge Computing Platform is bootstraped with 4GB of hugetables. 
You may have 16GB ram, thus you want to modify the amount of hugetables to 12GB.
To achieve that use the command below ( 6000 x 2 = ~ 12 GB)

```
config 
ecp general hugetables 6000
```

To display the available memory and hugetables type:

```
show ecp memory free-hugetables
```

To display the RAM status type:

```
show ecp memory free-memory
```


## Port identification and mapping

This command helps to identify the connected NICs. Node address + kernel driver (igb)

```
ecp list-nic-pci
```

To identify phy port of the NIC use this command (the phy port will be blinking):

```
ecp blink-port 0000:0a:00.0 igb // igb kernel driver for 1G NICs (check ecp list-nic-pci)
```

Next step to map pofixed DELETE operation: del port vm-port Xrts to addresses:

```
configure
set ecp interfaces vfpX pci-addr 0000:0a.00.0
```

## 2 types of interfaces

The interfaces below represent physical ports of the equipment 
```
/ecp/intefaces
```

The interfaces below represent virtual mgmnt ports connected with representation of physical ports (on the same link) 
They apper when representation of physical ports is configured on the switch. 

```
/interfaces/dataplane/dp0pX
```

## Service function configuration (qcow2 image)

```
config
set ecp vms sf1 boot-from hd
set ecp vms sf1 cpu 5
set ecp vms sf1 ram 4
set ecp vms sf1 devices 1 drive drive-type disk
set ecp vms sf1 devices 1 drive file 'http://10.0.0.2/vRouter.qcow2'
```

## Service function configuration (iso image)

```
config
set ecp vms sf1 boot-from hd
set ecp vms sf1 cpu 5
set ecp vms sf1 ram 4
set ecp vms sf1 devices 1 drive drive-type cdrom
set ecp vms sf1 devices 1 drive file 'http://10.0.0.2/vRouter.iso'
```

## Access to Service Functions

To list active Service Function type:

```
ecp vm-list
```

To access virtual serial console of Service Function type:

```
vm-console $SF_NAME
```

## vswitch configuration

Add physical port to switch. When the physical port added to switch, you will find new interface dp0pX in the /root/interfaces/dataplane. 
This dp0pX is configured for management. Confirm that it was configured by 

```
set ecp switches sw1 ports 1 interface vfp1
set ecp switches sw1 ports 2 vm sf1
set ecp switches sw1 ports 2 vm-port 1
```