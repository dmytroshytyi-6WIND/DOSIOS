## Welcome to [DOSIOS](https://dosios.shytyi.net) Page - Distributed Open Software Infrastructure Operating System

[![Hits](https://hits.seeyoufarm.com/api/count/incr/badge.svg?url=http%3A%2F%2Fdosios.shytyi.net&count_bg=%2379C83D&title_bg=%23555555&icon=&icon_color=%23E7E7E7&title=hits&edge_flat=false)](https://hits.seeyoufarm.com)
[![Gitter](https://badges.gitter.im/dmytroshytyi/dosiOS.svg)](https://gitter.im/dmytroshytyi/dosiOS?utm_source=badge&utm_medium=badge&utm_campaign=pr-badge)

With [DOSIOS](https://dosios.shytyi.net) you can:

* Create your service functions, chain them with virtual switches and physical ports of equipment. 
* Accelerate packet exchange with DPDK.
* Use NETCONF protocol and YANG modelisation language for dosiOS management. 
* Perform identification and physical port mapping on initial setup with provided toolkits.
* Set the parameters of service function deployment (cpu, ram, ifs, disks) and virtual switch configuration (trunk, access, tag, stacked-vlans).


![DOSIOS gui demo](https://github.com/dmytroshytyi/dosiOS-uCPE/blob/main/dosios2102-gui-service-chain.png?raw=true)

The figure below presents the High Availability deployement scenario of DOISOS.

The High Availability deployment scenario of DOISOS (with multiple Service Function Chained) between two distant sites involves the use of various components to ensure efficient and reliable network connectivity. The diagram illustrates the architecture and key elements involved in this scenario.

At the core of the deployment, there are two virtual routers (vRouters) located at each site. These vRouters act as the primary network gateways and are responsible for routing and forwarding data packets between the different services and clients. They provide the necessary connectivity and act as the termination points for multiple Internet Service Providers (ISPs) links.

To enhance the overlay functionality for clients, a virtual Software-Defined Wide Area Network (vSD-WAN) appliance is deployed. The vSD-WAN appliance enables the client to establish and manage a virtual overlay network that spans across the two distant sites. This overlay network ensures secure and optimized communication between the client's services and applications.

In this deployment scenario, the management responsibilities are distributed between the ISPs and the client. The ISPs are responsible for managing the vRouters, which includes tasks such as configuration, maintenance, and monitoring of the routers' functionality. On the other hand, the client manages the vSD-WAN appliance, taking care of its configuration, policies, and overall management without interfering with the vRouters' operations.

The main benefits of this deployment scenario include high availability, as multiple ISPs are utilized for link termination, ensuring redundancy and fault tolerance. The vRouters play a crucial role in maintaining connectivity between the sites and managing the traffic flow. The vSD-WAN appliance adds an additional layer of functionality, allowing the client to have granular control over their overlay network, optimizing performance and security.

Overall, this High Availability deployment scenario of DOISOS with multiple Service Function Chained architecture provides a robust and efficient network setup that meets the needs of distributed sites and allows for seamless connectivity and management of services and applications.


![DOSIOS HA deployment scenario](https://github.com/dmytroshytyi/DOSIOS/blob/main/doc/998_images/dosios_deployment_ha_mode.svg?raw=true)






