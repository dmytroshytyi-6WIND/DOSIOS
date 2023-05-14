## Welcome to DOSIOS Page - Distributed Open Software Infrastructure Operating System

[![Hits](https://hits.seeyoufarm.com/api/count/incr/badge.svg?url=http%3A%2F%2Fdosios.shytyi.net&count_bg=%2379C83D&title_bg=%23555555&icon=&icon_color=%23E7E7E7&title=hits&edge_flat=false)](https://hits.seeyoufarm.com)
[![Gitter](https://badges.gitter.im/dmytroshytyi/dosiOS.svg)](https://gitter.im/dmytroshytyi/dosiOS?utm_source=badge&utm_medium=badge&utm_campaign=pr-badge)

### Introduction
Distributed Open Software Infrastructure Operating System, or DOSIOS, is a powerful and flexible operating system that is designed to meet the needs of modern network infrastructures. 
With DOSIOS, you can easily create and deploy service functions, chain them together with virtual switches, and connect them to the physical ports of your network equipment.

One of the key features of DOSIOS is its support for the Data Plane Development Kit (DPDK), which allows for accelerated packet exchange and improved performance.
This means that your network can handle high volumes of traffic with ease, and can quickly and efficiently process data packets as they move through your network.


Another important aspect of DOSIOS is its support for the NETCONF protocol and YANG modelisation language.
This allows for easy management of your network infrastructure, and provides a standardized way to configure and manage network devices.

With DOSIOS, you can quickly and easily perform identification and physical port mapping during the initial setup phase, using the provided toolkits.
Additionally, DOSIOS allows you to set the parameters of your service function deployment, including CPU, RAM, IFs, and disks.
You can also configure your virtual switches with a range of options, including trunk, access, tag, and stacked VLANs.
This gives you complete control over your network infrastructure, and allows you to customize it to meet your specific needs and requirements.

In summary, DOSIOS is a powerful and flexible operating system that provides a range of tools and features for managing and deploying network services.
Whether you're a network administrator, an operator, a developer, or a system integrator, DOSIOS provides the tools you need to create a robust and scalable network infrastructure that can handle even the most demanding workloads.



![DOSIOS gui demo](https://github.com/dmytroshytyi/dosiOS-uCPE/blob/main/dosios2102-gui-service-chain.png?raw=true)


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

### Configuration of DOSIOS from NSO controller

We provide a Cisco NSO package [located in this repo](https://github.com/dmytroshytyi/dosiOS-mngt-by-Cisco-NSO) to controll DOSIOS software from CISCO NSO controller via [ietf-ucpe-yang modules](https://github.com/dmytroshytyi/ucpe-ietf)


### DOSIOS manual configuration

ecp == Edge Computing Platform

#### Login/Password for installed system:

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

Next step to map pofixed DELETE operation: del port vm-port Xrts to addresses:

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
### Configuration types and (supported)/(not_supported) commands

Configuration may be done via the CREATE, DELETE, CHANGE operations. Some of them supported more than anothers in current version.

#### CREATE

Most of creation commands except "cvlans" work as expected:

```
"cvlans" accepts only 1 cVLAN.
```

#### DELETE

MOST of deletion commands work as expected with reserve that CHANGES commands were not used.


#### CHANGE

This operation is a bit touchy. Some code modification currently under revision to support mode commands with CHANGE operation. 

```
CHANGE set ecp switches sw ports port X vm+port             => success 
CHANGE set ecp switches sw ports port X vm port1 -> port2   => success
CHANGE set ecp switches sw ports port X port-mode           => success 
CHANGE set ecp switches sw ports port X tag                 => success 
CHANGE set ecp switches sw ports port X stacked-mode        => success 
CHANGE set ecp switches sw ports port X n-txq               => success 
CHANGE set ecp switches sw ports port X n-rxq               => success 
CHANGE set ecp switches sw ports port X interface           => success 

```

### DOSIiOS IPERF3 test

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

Fixed restrictions in the YANG model.
Fixed commands to interact with OVS.

1. fixed CNAHGE operation: set port interface X
2. fixed CHANGE operation: set port vm-port X
3. fixed CHANGE operation: set port vm-port X => set port interface Y
4. fixed DELETE operation: del port vm-port X
5. fixed DELETE operation: del port vm X
6. fixed DELETE operation: del port X
7. Added Multiqueue configuration of the VNF and of the OVS.
8. Fixed multiple VNFs spawning at the same moment issue.
9. Added "commit failure/rollback" if VNF spawning isn't successfull (not enough memory, empty image, wrong link, etc..) 
