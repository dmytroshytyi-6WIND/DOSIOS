## Welcome to dosiOS Page - Edge Computing Platform

[![Hits](https://hits.seeyoufarm.com/api/count/incr/badge.svg?url=http%3A%2F%2Fdosios.shytyi.net&count_bg=%2379C83D&title_bg=%23555555&icon=&icon_color=%23E7E7E7&title=hits&edge_flat=false)](https://hits.seeyoufarm.com)
[![Gitter](https://badges.gitter.im/dmytroshytyi/dosiOS.svg)](https://gitter.im/dmytroshytyi/dosiOS?utm_source=badge&utm_medium=badge&utm_campaign=pr-badge)

Create your service functions, chain them with virtual switches and physical ports of equipment. Accelerate packet exchange with DPDK.

Use NETCONF protocol and YANG modelisation language for dosiOS management. Perform identification and physical port mapping on initial setup with provided toolkits.

Set the parameters of service function deployment (cpu, ram, ifs, disks) and virtual switch configuration (trunk, access, tag, stacked-vlans).


Available release is ALPHA version available for comments/issues/improvements.

To modify value in dosiOS (epc part of the tree) you need first to delete current value and after set new value. Modification of values not guaranteed and can lead to errors in current stage.

### Downloading

#### Link to download

Link to download the iso image is presented below

Date 30/08/2021_17:50

```
https://www.dropbox.com/s/3z6bjpv1hirydl0/dosios-2102.iso?dl=1
```

Date 30/05/2021_14:50
```
https://mega.nz/file/pMtUUDII
```

#### Decryption key (will be asked when start MEGA file download)

```
qcesj4PaNug7EWxn8puJAN_RZWcQ4rzljRdQVs3BCCY
```

### DEMO #1

[![IMAGE ALT TEXT HERE](https://img.youtube.com/vi/pRe4JbJ_eOI/0.jpg)](https://www.youtube.com/watch?v=pRe4JbJ_eOI)

### DEMO #2 

[![IMAGE ALT TEXT HERE](http://img.youtube.com/vi/T1cC2j_oey4/maxresdefault.jpg)](https://www.youtube.com/watch?v=T1cC2j_oey4)

### Installation
 
Repository provides iso image that should be flashed on the usb drive.
 
```
1. install qemu
2. qemu-img convert dosios2102.iso -O raw dosiOS-2102.img
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

### dosiOS configuration

ecp == Edge Computing Platform

### Login/Password for installed system:

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

#### Hugetalbes configuration

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


#### Port identification and mapping

This command helps to identify the connected NICs. Node address + kernel driver (igb)

```
ecp list-nic-pci
```

To identify phy port of the NIC use this command (the phy port will be blinking):

```
ecp blink-port 0000:0a:00.0 igb // igb kernel driver for 1G NICs (check ecp list-nic-pci)
```

Next step to map ports to addresses:

```
configure
set ecp interfaces vfpX pci-addr 0000:0a.00.0
```

#### 2 types of interfaces

The interfaces below represent physical ports of the equipment 
```
/ecp/intefaces
```

The interfaces below represent virtual mgmnt ports connected with representation of physical ports (on the same link) 
They apper when representation of physical ports is configured on the switch. 

```
/interfaces/dataplane/dp0pX
```

#### Service function configuration (qcow2 image)

```
config
set ecp vms sf1 boot-from hd
set ecp vms sf1 cpu 5
set ecp vms sf1 ram 4
set ecp vms sf1 devices 1 drive drive-type disk
set ecp vms sf1 devices 1 drive file 'http://10.0.0.2/vRouter.qcow2'
```

#### Service function configuration (iso image)

```
config
set ecp vms sf1 boot-from hd
set ecp vms sf1 cpu 5
set ecp vms sf1 ram 4
set ecp vms sf1 devices 1 drive drive-type cdrom
set ecp vms sf1 devices 1 drive file 'http://10.0.0.2/vRouter.iso'
```

### Access to Service Functions

To list active Service Function type:

```
ecp vm-list
```

To access virtual serial console of Service Function type:

```
vm-console $SF_NAME
```

#### vswitch configuration

Add physical port to switch. When the physical port added to switch, you will find new interface dp0pX in the /root/interfaces/dataplane. 
This dp0pX is configured for management. Confirm that it was configured by 

```
set ecp switches sw1 ports 1 interface vfp1
set ecp switches sw1 ports 2 vm sf1
set ecp switches sw1 ports 2 vm-port 1
```

### dosiOS IPERF3 test

Conditions:

```

VM(vRouter) - sw1 - VM(vRouter)
```

Result

![dosiOS benchmark](https://github.com/dmytroshytyi/dosiOS-uCPE/blob/main/dosiOS-VM-to-VM-performance.png?raw=true)



```

Traffic generator -- eth1(1G) -- sw1 -- sf(router1) -- sw2 -- sf(router2) -- sw3 -- eth10(1G) -- Traffic generator
```

Result 

![dosiOS benchmark](https://github.com/dmytroshytyi/dosiOS/blob/main/dosiOS-bench.PNG?raw=true)


### Changelog

#### Date 30/08/2021_17:50

1. Added Multiqueue configuration of the VNF and of the OVS.
2. Fixed multiple VNFs spawning at the same moment issue.
3. Added "commit failure/rollback" if VNF spawning isn't successfull (not enough memory, empty image, wrong link, etc..) 
