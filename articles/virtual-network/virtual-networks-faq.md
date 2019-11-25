---
title: Azure Virtual Network FAQ
titlesuffix: Azure Virtual Network
description: Answers to the most frequently asked questions about Microsoft Azure virtual networks.
services: virtual-network
documentationcenter: na
author: KumudD
manager: twooley
ms.service: virtual-network
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 02/12/2019
ms.author: kumud
ms.openlocfilehash: 5fae340ae933b8165a2ea9bb9f6337189fd576d6
ms.sourcegitcommit: 12d902e78d6617f7e78c062bd9d47564b5ff2208
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 11/24/2019
ms.locfileid: "74457033"
---
# <a name="azure-virtual-network-frequently-asked-questions-faq"></a>Azure Virtual Network frequently asked questions (FAQ)

## <a name="virtual-network-basics"></a>Virtual Network basics

### <a name="what-is-an-azure-virtual-network-vnet"></a>What is an Azure Virtual Network (VNet)?
An Azure Virtual Network (VNet) is a representation of your own network in the cloud. É um isolamento lógico da nuvem do Azure dedicado à sua subscrição. You can use VNets to provision and manage virtual private networks (VPNs) in Azure and, optionally, link the VNets with other VNets in Azure, or with your on-premises IT infrastructure to create hybrid or cross-premises solutions. Each VNet you create has its own CIDR block and can be linked to other VNets and on-premises networks as long as the CIDR blocks do not overlap. You also have control of DNS server settings for VNets, and segmentation of the VNet into subnets.

Use VNets to:

* Create a dedicated private cloud-only VNet. Sometimes you don't require a cross-premises configuration for your solution. When you create a VNet, your services and VMs within your VNet can communicate directly and securely with each other in the cloud. You can still configure endpoint connections for the VMs and services that require Internet communication, as part of your solution.

* Securely extend your data center. With VNets, you can build traditional site-to-site (S2S) VPNs to securely scale your datacenter capacity. S2S VPNs use IPSEC to provide a secure connection between your corporate VPN gateway and Azure.

* Enable hybrid cloud scenarios. VNets give you the flexibility to support a range of hybrid cloud scenarios. You can securely connect cloud-based applications to any type of on-premises system such as mainframes and Unix systems.

### <a name="how-do-i-get-started"></a>Como começo?
Visit the [Virtual network documentation](https://docs.microsoft.com/azure/virtual-network/) to get started. This content provides overview and deployment information for all of the VNet features.

### <a name="can-i-use-vnets-without-cross-premises-connectivity"></a>Can I use VNets without cross-premises connectivity?
Sim. You can use a VNet without connecting it to your premises. For example, you could run Microsoft Windows Server Active Directory domain controllers and SharePoint farms solely in an Azure VNet.

### <a name="can-i-perform-wan-optimization-between-vnets-or-a-vnet-and-my-on-premises-data-center"></a>Can I perform WAN optimization between VNets or a VNet and my on-premises data center?
Sim. You can deploy a [WAN optimization network virtual appliance](https://azuremarketplace.microsoft.com/en-us/marketplace/?term=wan%20optimization) from several vendors through the Azure Marketplace.

## <a name="configuration"></a>Configuração

### <a name="what-tools-do-i-use-to-create-a-vnet"></a>What tools do I use to create a VNet?
You can use the following tools to create or configure a VNet:

* Portal do Azure
* PowerShell
* CLI do Azure
* A network configuration file (netcfg - for classic VNets only). See the [Configure a VNet using a network configuration file](virtual-networks-using-network-configuration-file.md) article.

### <a name="what-address-ranges-can-i-use-in-my-vnets"></a>What address ranges can I use in my VNets?
Any IP address range defined in [RFC 1918](https://tools.ietf.org/html/rfc1918). For example, 10.0.0.0/16. You cannot add the following address ranges:
* 224.0.0.0/4 (Multicast)
* 255.255.255.255/32 (Broadcast)
* 127.0.0.0/8 (Loopback)
* 169.254.0.0/16 (Link-local)
* 168.63.129.16/32 (Internal DNS)

### <a name="can-i-have-public-ip-addresses-in-my-vnets"></a>Can I have public IP addresses in my VNets?
Sim. For more information about public IP address ranges, see [Create a virtual network](manage-virtual-network.md#create-a-virtual-network). Public IP addresses are not directly accessible from the internet.

### <a name="is-there-a-limit-to-the-number-of-subnets-in-my-vnet"></a>Is there a limit to the number of subnets in my VNet?
Sim. See [Azure limits](../azure-subscription-service-limits.md?toc=%2fazure%2fvirtual-network%2ftoc.json#networking-limits) for details. Subnet address spaces cannot overlap one another.

### <a name="are-there-any-restrictions-on-using-ip-addresses-within-these-subnets"></a>Are there any restrictions on using IP addresses within these subnets?
Sim. O Azure reserva cinco endereços IP em cada sub-rede. These are x.x.x.0-x.x.x.3 and the last address of the subnet. x.x.x.1-x.x.x.3 is reserved in each subnet for Azure services.   
- x.x.x.0: Network address
- x.x.x.1: Reserved by Azure for the default gateway
- x.x.x.2, x.x.x.3: Reserved by Azure to map the Azure DNS IPs to the VNet space
- x.x.x.255: Network broadcast address

### <a name="how-small-and-how-large-can-vnets-and-subnets-be"></a>How small and how large can VNets and subnets be?
The smallest supported IPv4 subnet is /29, and the largest is /8 (using CIDR subnet definitions).  IPv6 subnets must be exactly /64 in size.  

### <a name="can-i-bring-my-vlans-to-azure-using-vnets"></a>Can I bring my VLANs to Azure using VNets?
Não. VNets are Layer-3 overlays. Azure does not support any Layer-2 semantics.

### <a name="can-i-specify-custom-routing-policies-on-my-vnets-and-subnets"></a>Can I specify custom routing policies on my VNets and subnets?
Sim. You can create a route table and associate it to a subnet. For more information about routing in Azure, see [Routing overview](virtual-networks-udr-overview.md#custom-routes).

### <a name="do-vnets-support-multicast-or-broadcast"></a>Do VNets support multicast or broadcast?
Não. Multicast and broadcast are not supported.

### <a name="what-protocols-can-i-use-within-vnets"></a>What protocols can I use within VNets?
You can use TCP, UDP, and ICMP TCP/IP protocols within VNets. Unicast is supported within VNets, with the exception of Dynamic Host Configuration Protocol (DHCP) via Unicast (source port UDP/68 / destination port UDP/67). Multicast, broadcast, IP-in-IP encapsulated packets, and Generic Routing Encapsulation (GRE) packets are blocked within VNets. 

### <a name="can-i-ping-my-default-routers-within-a-vnet"></a>Can I ping my default routers within a VNet?
Não.

### <a name="can-i-use-tracert-to-diagnose-connectivity"></a>Can I use tracert to diagnose connectivity?
Não.

### <a name="can-i-add-subnets-after-the-vnet-is-created"></a>Can I add subnets after the VNet is created?
Sim. Subnets can be added to VNets at any time as long as the subnet address range is not part of another subnet and there is available space left in the virtual network's address range.

### <a name="can-i-modify-the-size-of-my-subnet-after-i-create-it"></a>Can I modify the size of my subnet after I create it?
Sim. You can add, remove, expand, or shrink a subnet if there are no VMs or services deployed within it.

### <a name="can-i-modify-subnets-after-i-created-them"></a>Can I modify subnets after I created them?
Sim. You can add, remove, and modify the CIDR blocks used by a VNet.

### <a name="if-i-am-running-my-services-in-a-vnet-can-i-connect-to-the-internet"></a>If I am running my services in a VNet, can I connect to the internet?
Sim. All services deployed within a VNet can connect outbound to the internet. To learn more about outbound internet connections in Azure, see [Outbound connections](../load-balancer/load-balancer-outbound-connections.md?toc=%2fazure%2fvirtual-network%2ftoc.json). If you want to connect inbound to a resource deployed through Resource Manager, the resource must have a public IP address assigned to it. To learn more about public IP addresses, see [Public IP addresses](virtual-network-public-ip-address.md). Every Azure Cloud Service deployed in Azure has a publicly addressable VIP assigned to it. You define input endpoints for PaaS roles and endpoints for virtual machines to enable these services to accept connections from the internet.

### <a name="do-vnets-support-ipv6"></a>Do VNets support IPv6?
Yes, VNets can be IPv4-only or dual stack (IPv4+IPv6).  For details, see [Overview of IPv6 for Azure Virtual Networks](./ipv6-overview.md).

### <a name="can-a-vnet-span-regions"></a>Can a VNet span regions?
Não. A VNet is limited to a single region. A virtual network does, however, span availability zones. Para saber mais sobre as zonas de disponibilidade, veja [Descrição geral das zonas de disponibilidade](../availability-zones/az-overview.md?toc=%2fazure%2fvirtual-network%2ftoc.json). You can connect virtual networks in different regions with virtual network peering. For details, see [Virtual network peering overview](virtual-network-peering-overview.md)

### <a name="can-i-connect-a-vnet-to-another-vnet-in-azure"></a>Can I connect a VNet to another VNet in Azure?
Sim. You can connect one VNet to another VNet using either:
- **Virtual network peering**: For details, see [VNet peering overview](virtual-network-peering-overview.md)
- **An Azure VPN Gateway**: For details, see [Configure a VNet-to-VNet connection](../vpn-gateway/vpn-gateway-howto-vnet-vnet-resource-manager-portal.md?toc=%2fazure%2fvirtual-network%2ftoc.json). 

## <a name="name-resolution-dns"></a>Name Resolution (DNS)

### <a name="what-are-my-dns-options-for-vnets"></a>What are my DNS options for VNets?
Use the decision table on the [Name Resolution for VMs and Role Instances](virtual-networks-name-resolution-for-vms-and-role-instances.md) page to guide you through all the DNS options available.

### <a name="can-i-specify-dns-servers-for-a-vnet"></a>Can I specify DNS servers for a VNet?
Sim. You can specify DNS server IP addresses in the VNet settings. The setting is applied as the default DNS server(s) for all VMs in the VNet.

### <a name="how-many-dns-servers-can-i-specify"></a>How many DNS servers can I specify?
Reference [Azure limits](../azure-subscription-service-limits.md?toc=%2fazure%2fvirtual-network%2ftoc.json#networking-limits).

### <a name="can-i-modify-my-dns-servers-after-i-have-created-the-network"></a>Can I modify my DNS servers after I have created the network?
Sim. You can change the DNS server list for your VNet at any time. If you change your DNS server list, you need to perform a DHCP lease renewal on all affected VMs in the VNet, for the new DNS settings to take effect. For VMs running Windows OS you can do this by typing `ipconfig /renew` directly on the VM. For other OS types, refer to the DHCP lease renewal documentation for the specific OS type. 

### <a name="what-is-azure-provided-dns-and-does-it-work-with-vnets"></a>What is Azure-provided DNS and does it work with VNets?
Azure-provided DNS is a multi-tenant DNS service offered by Microsoft. Azure registers all of your VMs and cloud service role instances in this service. This service provides name resolution by hostname for VMs and role instances contained within the same cloud service, and by FQDN for VMs and role instances in the same VNet. To learn more about DNS, see [Name Resolution for VMs and Cloud Services role instances](virtual-networks-name-resolution-for-vms-and-role-instances.md).

There is a limitation to the first 100 cloud services in a VNet for cross-tenant name resolution using Azure-provided DNS. If you are using your own DNS server, this limitation does not apply.

### <a name="can-i-override-my-dns-settings-on-a-per-vm-or-cloud-service-basis"></a>Can I override my DNS settings on a per-VM or cloud service basis?
Sim. You can set DNS servers per VM or cloud service to override the default network settings. However, it's recommended that you use network-wide DNS as much as possible.

### <a name="can-i-bring-my-own-dns-suffix"></a>Can I bring my own DNS suffix?
Não. You cannot specify a custom DNS suffix for your VNets.

## <a name="connecting-virtual-machines"></a>Connecting virtual machines

### <a name="can-i-deploy-vms-to-a-vnet"></a>Can I deploy VMs to a VNet?
Sim. All network interfaces (NIC) attached to a VM deployed through the Resource Manager deployment model must be connected to a VNet. VMs deployed through the classic deployment model can optionally be connected to a VNet.

### <a name="what-are-the-different-types-of-ip-addresses-i-can-assign-to-vms"></a>What are the different types of IP addresses I can assign to VMs?
* **Private:** Assigned to each NIC within each VM. The address is assigned using either the static or dynamic method. Private IP addresses are assigned from the range that you specified in the subnet settings of your VNet. Resources deployed through the classic deployment model are assigned private IP addresses, even if they're not connected to a VNet. The behavior of the allocation method is different depending on whether a resource was deployed with the Resource Manager or classic deployment model: 

  - **Resource Manager**: A private IP address assigned with the dynamic or static method remains assigned to a virtual machine (Resource Manager) until the resource is deleted. The difference is that you select the address to assign when using static, and Azure chooses when using dynamic. 
  - **Classic**: A private IP address assigned with the dynamic method may change when a virtual machine (classic) VM is restarted after having been in the stopped (deallocated) state. If you need to ensure that the private IP address for a resource deployed through the classic deployment model never changes, assign a private IP address with the static method.

* **Public:** Optionally assigned to NICs attached to VMs deployed through the Azure Resource Manager deployment model. The address can be assigned with the static or dynamic allocation method. All VMs and Cloud Services role instances deployed through the classic deployment model exist within a cloud service, which is assigned a *dynamic*, public virtual IP (VIP) address. A public *static* IP address, called a [Reserved IP address](virtual-networks-reserved-public-ip.md), can optionally be assigned as a VIP. You can assign public IP addresses to individual VMs or Cloud Services role instances deployed through the classic deployment model. These addresses are called [Instance level public IP (ILPIP](virtual-networks-instance-level-public-ip.md) addresses and can be assigned dynamically.

### <a name="can-i-reserve-a-private-ip-address-for-a-vm-that-i-will-create-at-a-later-time"></a>Can I reserve a private IP address for a VM that I will create at a later time?
Não. You cannot reserve a private IP address. If a private IP address is available, it is assigned to a VM or role instance by the DHCP server. The VM may or may not be the one that you want the private IP address assigned to. You can, however, change the private IP address of an already created VM, to any available private IP address.

### <a name="do-private-ip-addresses-change-for-vms-in-a-vnet"></a>Do private IP addresses change for VMs in a VNet?
Depende. If the VM was deployed through Resource Manager, no, regardless of whether the IP address was assigned with the static or dynamic allocation method. If the VM was deployed through the classic deployment model, dynamic IP addresses can change when a VM is started after having been in the stopped (deallocated) state. The address is released from a VM deployed through either deployment model when the VM is deleted.

### <a name="can-i-manually-assign-ip-addresses-to-nics-within-the-vm-operating-system"></a>Can I manually assign IP addresses to NICs within the VM operating system?
Yes, but it's not recommended unless necessary, such as when assigning multiple IP addresses to a virtual machine. For details, see [Adding multiple IP addresses to a virtual machine](virtual-network-multiple-ip-addresses-portal.md#os-config). If the IP address assigned to an Azure NIC attached to a VM changes, and the IP address within the VM operating system is different, you lose connectivity to the VM.

### <a name="if-i-stop-a-cloud-service-deployment-slot-or-shutdown-a-vm-from-within-the-operating-system-what-happens-to-my-ip-addresses"></a>If I stop a Cloud Service deployment slot or shutdown a VM from within the operating system, what happens to my IP addresses?
Nothing. The IP addresses (public VIP, public, and private) remain assigned to the cloud service deployment slot or VM.

### <a name="can-i-move-vms-from-one-subnet-to-another-subnet-in-a-vnet-without-redeploying"></a>Can I move VMs from one subnet to another subnet in a VNet without redeploying?
Sim. You can find more information in the [How to move a VM or role instance to a different subnet](virtual-networks-move-vm-role-to-subnet.md) article.

### <a name="can-i-configure-a-static-mac-address-for-my-vm"></a>Can I configure a static MAC address for my VM?
Não. A MAC address cannot be statically configured.

### <a name="will-the-mac-address-remain-the-same-for-my-vm-once-its-created"></a>Will the MAC address remain the same for my VM once it's created?
Yes, the MAC address remains the same for a VM deployed through both the Resource Manager and classic deployment models until it's deleted. Previously, the MAC address was released if the VM was stopped (deallocated), but now the MAC address is retained even when the VM is in the deallocated state. The MAC address remains assigned to the network interface until the network interface is deleted or the private IP address assigned to the primary IP configuration of the primary network interface is changed. 

### <a name="can-i-connect-to-the-internet-from-a-vm-in-a-vnet"></a>Can I connect to the internet from a VM in a VNet?
Sim. All VMs and Cloud Services role instances deployed within a VNet can connect to the Internet.

## <a name="azure-services-that-connect-to-vnets"></a>Azure services that connect to VNets

### <a name="can-i-use-azure-app-service-web-apps-with-a-vnet"></a>Can I use Azure App Service Web Apps with a VNet?
Sim. You can deploy Web Apps inside a VNet using an ASE (App Service Environment), connect the backend of your apps to your VNets with VNet Integration, and lock down inbound traffic to your app with service endpoints. Para obter mais informações, veja os artigos seguintes:

* [App Service networking features](../app-service/networking-features.md)
* [Creating Web Apps in an App Service Environment](../app-service/environment/app-service-web-how-to-create-a-web-app-in-an-ase.md?toc=%2fazure%2fvirtual-network%2ftoc.json)
* [Integrar uma aplicação Web com uma Rede Virtual do Azure](../app-service/web-sites-integrate-with-vnet.md?toc=%2fazure%2fvirtual-network%2ftoc.json)
* [App Service access restrictions](../app-service/app-service-ip-restrictions.md)

### <a name="can-i-deploy-cloud-services-with-web-and-worker-roles-paas-in-a-vnet"></a>Can I deploy Cloud Services with web and worker roles (PaaS) in a VNet?
Sim. You can (optionally) deploy Cloud Services role instances within VNets. To do so, you specify the VNet name and the role/subnet mappings in the network configuration section of your service configuration. You do not need to update any of your binaries.

### <a name="can-i-connect-a-virtual-machine-scale-set-to-a-vnet"></a>Can I connect a virtual machine scale set to a VNet?
Sim. You must connect a virtual machine scale set to a VNet.

### <a name="is-there-a-complete-list-of-azure-services-that-can-i-deploy-resources-from-into-a-vnet"></a>Is there a complete list of Azure services that can I deploy resources from into a VNet?
Yes, For details, see [Virtual network integration for Azure services](virtual-network-for-azure-services.md).

### <a name="how-can-i-restrict-access-to-azure-paas-resources-from-a-vnet"></a>How can I restrict access to Azure PaaS resources from a VNet?

Resources deployed through some Azure PaaS services (such as Azure Storage and Azure SQL Database), can restrict network access to VNet through the use of virtual network service endpoints or Azure Private Link. For details, see [Virtual network service endpoints overview](virtual-network-service-endpoints-overview.md), [Azure Private Link overview](../private-link/private-link-overview.md)

### <a name="can-i-move-my-services-in-and-out-of-vnets"></a>Can I move my services in and out of VNets?
Não. You cannot move services in and out of VNets. To move a resource to another VNet, you have to delete and redeploy the resource.

## <a name="security"></a>Segurança

### <a name="what-is-the-security-model-for-vnets"></a>What is the security model for VNets?
VNets are isolated from one another, and other services hosted in the Azure infrastructure. A VNet is a trust boundary.

### <a name="can-i-restrict-inbound-or-outbound-traffic-flow-to-vnet-connected-resources"></a>Can I restrict inbound or outbound traffic flow to VNet-connected resources?
Sim. You can apply [Network Security Groups](security-overview.md) to individual subnets within a VNet, NICs attached to a VNet, or both.

### <a name="can-i-implement-a-firewall-between-vnet-connected-resources"></a>Can I implement a firewall between VNet-connected resources?
Sim. You can deploy a [firewall network virtual appliance](https://azure.microsoft.com/marketplace/?term=firewall) from several vendors through the Azure Marketplace.

### <a name="is-there-information-available-about-securing-vnets"></a>Is there information available about securing VNets?
Sim. For details, see [Azure Network Security Overview](../security/fundamentals/network-overview.md?toc=%2fazure%2fvirtual-network%2ftoc.json).

## <a name="apis-schemas-and-tools"></a>APIs, schemas, and tools

### <a name="can-i-manage-vnets-from-code"></a>Can I manage VNets from code?
Sim. You can use REST APIs for VNets in the [Azure Resource Manager](/rest/api/virtual-network) and [classic](https://go.microsoft.com/fwlink/?LinkId=296833) deployment models.

### <a name="is-there-tooling-support-for-vnets"></a>Is there tooling support for VNets?
Sim. Learn more about using:
- The Azure portal to deploy VNets through the [Azure Resource Manager](manage-virtual-network.md#create-a-virtual-network) and [classic](virtual-networks-create-vnet-classic-pportal.md) deployment models.
- PowerShell to manage VNets deployed through the [Resource Manager](/powershell/module/az.network) and [classic](/powershell/module/servicemanagement/azure/?view=azuresmps-3.7.0) deployment models.
- The Azure command-line interface (CLI) to deploy and manage VNets deployed through the [Resource Manager](/cli/azure/network/vnet) and [classic](../virtual-machines/azure-cli-arm-commands.md?toc=%2fazure%2fvirtual-network%2ftoc.json#azure-network-commands-to-manage-network-resources) deployment models.  

## <a name="vnet-peering"></a>VNet Peering

### <a name="what-is-vnet-peering"></a>What is VNet peering?
VNet peering (or virtual network peering) enables you to connect virtual networks. A VNet peering connection between virtual networks enables you to route traffic between them privately through IPv4 addresses. Virtual machines in the peered VNets can communicate with each other as if they are within the same network. These virtual networks can be in the same region or in different regions (also known as Global VNet Peering). VNet peering connections can also be created across Azure subscriptions.

### <a name="can-i-create-a-peering-connection-to-a-vnet-in-a-different-region"></a>Can I create a peering connection to a VNet in a different region?
Sim. Global VNet peering enables you to peer VNets in different regions. Global VNet peering is available in all Azure public regions, China cloud regions, and Government cloud regions. You cannot globally peer from Azure public regions to national cloud regions.

### <a name="what-are-the-constraints-related-to-global-vnet-peering-and-load-balancers"></a>What are the constraints related to Global VNet Peering and Load Balancers?
If the two virtual networks in two different regions are peered over Global VNet Peering, you cannot connect to resources that are behind a Basic Load Balancer through the Front End IP of the Load Balancer. This restriction does not exist for a Standard Load Balancer.
The following resources can use Basic Load Balancers which means you cannot reach them through the Load Balancer's Front End IP over Global VNet Peering. You can however use Global VNet peering to reach the resources directly through their private VNet IPs, if permitted. 
- VMs behind Basic Load Balancers
- Virtual machine scale sets with Basic Load Balancers 
- Cache de Redis 
- Application Gateway (v1) SKU
- Service Fabric
- SQL MI
- Gestão de API
- Active Directory Domain Service (ADDS)
- Logic Apps
- HDInsight
-   Azure Batch
- Ambiente do Serviço de Aplicações

You can connect to these resources via ExpressRoute or VNet-to-VNet through VNet Gateways.

### <a name="can-i-enable-vnet-peering-if-my-virtual-networks-belong-to-subscriptions-within-different-azure-active-directory-tenants"></a>Can I enable VNet Peering if my virtual networks belong to subscriptions within different Azure Active Directory tenants?
Sim. It is possible to establish VNet Peering (whether local or global) if your subscriptions belong to different Azure Active Directory tenants. You can do this via PowerShell or CLI. Portal is not yet supported.

### <a name="my-vnet-peering-connection-is-in-initiated-state-why-cant-i-connect"></a>My VNet peering connection is in *Initiated* state, why can't I connect?
If your peering connection is in an *Initiated* state, this means you have created only one link. A bidirectional link must be created in order to establish a successful connection. For example, to peer VNet A to VNet B, a link must be created from VNetA to VNetB and from VNetB to VNetA. Creating both links will change the state to *Connected*.

### <a name="my-vnet-peering-connection-is-in-disconnected-state-why-cant-i-create-a-peering-connection"></a>My VNet peering connection is in *Disconnected* state, why can't I create a peering connection?
If your VNet peering connection is in a *Disconnected* state, it means one of the links created was deleted. In order to re-establish a peering connection, you will need to delete the link and recreate it.

### <a name="can-i-peer-my-vnet-with-a-vnet-in-a-different-subscription"></a>Can I peer my VNet with a VNet in a different subscription?
Sim. You can peer VNets across subscriptions and across regions.

### <a name="can-i-peer-two-vnets-with-matching-or-overlapping-address-ranges"></a>Can I peer two VNets with matching or overlapping address ranges?
Não. Address spaces must not overlap to enable VNet Peering.

### <a name="how-much-do-vnet-peering-links-cost"></a>How much do VNet peering links cost?
There is no charge for creating a VNet peering connection. Data transfer across peering connections is charged. [See here](https://azure.microsoft.com/pricing/details/virtual-network/).

### <a name="is-vnet-peering-traffic-encrypted"></a>Is VNet peering traffic encrypted?
Não. Traffic between resources in peered VNets is private and isolated. It remains completely on the Microsoft Backbone.

### <a name="why-is-my-peering-connection-in-a-disconnected-state"></a>Why is my peering connection in a *Disconnected* state?
VNet peering connections go into *Disconnected* state when one VNet peering link is deleted. You must delete both links in order to reestablish a successful peering connection.

### <a name="if-i-peer-vneta-to-vnetb-and-i-peer-vnetb-to-vnetc-does-that-mean-vneta-and-vnetc-are-peered"></a>If I peer VNetA to VNetB and I peer VNetB to VNetC, does that mean VNetA and VNetC are peered?
Não. Transitive peering is not supported. You must peer VNetA and VNetC for this to take place.

### <a name="are-there-any-bandwidth-limitations-for-peering-connections"></a>Are there any bandwidth limitations for peering connections?
Não. VNet peering, whether local or global, does not impose any bandwidth restrictions. Bandwidth is only limited by the VM or the compute resource.

### <a name="how-can-i-troubleshoot-vnet-peering-issues"></a>How can I troubleshoot VNet Peering issues?
Here is a [troubleshooter guide](https://support.microsoft.com/en-us/help/4486956/troubleshooter-for-virtual-network-peering-issues) you can try.

## <a name="virtual-network-tap"></a>TAP de rede virtual

### <a name="which-azure-regions-are-available-for-virtual-network-tap"></a>Which Azure regions are available for virtual network TAP?
Virtual network TAP preview is available in all Azure regions. The monitored network interfaces, the virtual network TAP resource, and the collector or analytics solution must be deployed in the same region.

### <a name="does-virtual-network-tap-support-any-filtering-capabilities-on-the-mirrored-packets"></a>Does Virtual Network TAP support any filtering capabilities on the mirrored packets?
Filtering capabilities are not supported with the virtual network TAP preview. When a TAP configuration is added to a network interface a deep copy of all the ingress and egress traffic on the network interface is streamed to the TAP destination.

### <a name="can-multiple-tap-configurations-be-added-to-a-monitored-network-interface"></a>Can multiple TAP configurations be added to a monitored network interface?
A monitored network interface can have only one TAP configuration. Check with the individual [partner solution](virtual-network-tap-overview.md#virtual-network-tap-partner-solutions) for the capability to stream multiple copies of the TAP traffic to the analytics tools of your choice.

### <a name="can-the-same-virtual-network-tap-resource-aggregate-traffic-from-monitored-network-interfaces-in-more-than-one-virtual-network"></a>Can the same virtual network TAP resource aggregate traffic from monitored network interfaces in more than one virtual network?
Sim. The same virtual network TAP resource can be used to aggregate mirrored traffic from monitored network interfaces in peered virtual networks in the same subscription or a different subscription. The virtual network TAP resource and the destination load balancer or destination network interface must be in the same subscription. All subscriptions must be under the same Azure Active Directory tenant.

### <a name="are-there-any-performance-considerations-on-production-traffic-if-i-enable-a-virtual-network-tap-configuration-on-a-network-interface"></a>Are there any performance considerations on production traffic if I enable a virtual network TAP configuration on a network interface?

Virtual network TAP is in preview. During preview, there is no service level agreement. The capability should not be used for production workloads. When a virtual machine network interface is enabled with a TAP configuration, the same resources on the Azure host allocated to the virtual machine to send the production traffic is used to perform the mirroring function and send the mirrored packets. Select the correct [Linux](../virtual-machines/linux/sizes.md?toc=%2fazure%2fvirtual-network%2ftoc.json) or [Windows](../virtual-machines/linux/sizes.md?toc=%2fazure%2fvirtual-network%2ftoc.json) virtual machine size to ensure that sufficient resources are available for the virtual machine to send the production traffic and the mirrored traffic.

### <a name="is-accelerated-networking-for-linuxcreate-vm-accelerated-networking-climd-or-windowscreate-vm-accelerated-networking-powershellmd-supported-with-virtual-network-tap"></a>Is accelerated networking for [Linux](create-vm-accelerated-networking-cli.md) or [Windows](create-vm-accelerated-networking-powershell.md) supported with virtual network TAP?

You will be able to add a TAP configuration on a network interface attached to a virtual machine that is enabled with accelerated networking. But the performance and latency on the virtual machine will be affected by adding TAP configuration since the offload for mirroring traffic is currently not supported by Azure accelerated networking.

## <a name="virtual-network-service-endpoints"></a>Pontos finais de serviço de rede virtual

### <a name="what-is-the-right-sequence-of-operations-to-set-up-service-endpoints-to-an-azure-service"></a>What is the right sequence of operations to set up service endpoints to an Azure service?
There are two steps to secure an Azure service resource through service endpoints:
1. Turn on service endpoints for the Azure service.
2. Set up VNet ACLs on the Azure service.

The first step is a network side operation and the second step is a service resource side operation. Both steps can be performed either by the same administrator or different administrators based on the RBAC permissions granted to the administrator role. We recommend that you first turn on service endpoints for your virtual network prior to setting up VNet ACLs on Azure service side. Hence, the steps must be performed in the sequence listed above to set up VNet service endpoints.

>[!NOTE]
> Both the operations described above must be completed before you can limit the Azure service access to the allowed VNet and subnet. Only turning on service endpoints for the Azure service on the network side does not provide you the limited access. In addition, you must also set up VNet ACLs on the Azure service side.

Certain services (such as SQL and CosmosDB) allow exceptions to the above sequence through the **IgnoreMissingVnetServiceEndpoint** flag. Once the flag is set to **True**, VNet ACLs can be set on the Azure service side prior to setting up the service endpoints on the network side. Azure services provide this flag to help customers in cases where the specific IP firewalls are configured on Azure services and turning on the service endpoints on the network side can lead to a connectivity drop since the source IP changes from a public IPv4 address to a private address. Setting up VNet ACLs on the Azure service side before setting service endpoints on the network side can help avoid a connectivity drop.

### <a name="do-all-azure-services-reside-in-the-azure-virtual-network-provided-by-the-customer-how-does-vnet-service-endpoint-work-with-azure-services"></a>Do all Azure services reside in the Azure virtual network provided by the customer? How does VNet service endpoint work with Azure services?

No, not all Azure services reside in the customer's virtual network. The majority of Azure data services such as Azure Storage, Azure SQL, and Azure Cosmos DB, are multi-tenant services that can be accessed over public IP addresses. You can learn more about virtual network integration for Azure services [here](virtual-network-for-azure-services.md). 

When you use the VNet service endpoints feature (turning on VNet service endpoint on the network side and setting up appropriate VNet ACLs on the Azure service side), access to an Azure service is restricted from an allowed VNet and subnet.

### <a name="how-does-vnet-service-endpoint-provide-security"></a>How does VNet service endpoint provide security?

The VNet service endpoint feature (turning on VNet service endpoint on the network side and setting up appropriate VNet ACLs on the Azure service side) limits the Azure service access to the allowed VNet and subnet, thus providing a network level security and isolation of the Azure service traffic. All traffic using VNet service endpoints flows over Microsoft backbone, thus providing another layer of isolation from the public internet. Moreover, customers can choose to fully remove public Internet access to the Azure service resources and allow traffic only from their virtual network through a combination of IP firewall and VNet ACLs, thus protecting the Azure service resources from unauthorized access.      

### <a name="what-does-the-vnet-service-endpoint-protect---vnet-resources-or-azure-service"></a>What does the VNet service endpoint protect - VNet resources or Azure service?
VNet service endpoints help protect Azure service resources. VNet resources are protected through Network Security Groups (NSGs).

### <a name="is-there-any-cost-for-using-vnet-service-endpoints"></a>Is there any cost for using VNet service endpoints?

No, there is no additional cost for using VNet service endpoints.

### <a name="can-i-turn-on-vnet-service-endpoints-and-set-up-vnet-acls-if-the-virtual-network-and-the-azure-service-resources-belong-to-different-subscriptions"></a>Can I turn on VNet service endpoints and set up VNet ACLs if the virtual network and the Azure service resources belong to different subscriptions?

Sim, é possível. Virtual networks and Azure service resources can be either in the same or different subscriptions. The only requirement is that both the virtual network and Azure service resources must be under the same Active Directory (AD) tenant.

### <a name="can-i-turn-on-vnet-service-endpoints-and-set-up-vnet-acls-if-the-virtual-network-and-the-azure-service-resources-belong-to-different-ad-tenants"></a>Can I turn on VNet service endpoints and set up VNet ACLs if the virtual network and the Azure service resources belong to different AD tenants?
No, VNet service endpoints and VNet ACLs are not supported across AD tenants.

### <a name="can-an-on-premises-devices-ip-address-that-is-connected-through-azure-virtual-network-gateway-vpn-or-expressroute-gateway-access-azure-paas-service-over-vnet-service-endpoints"></a>Can an on-premises device’s IP address that is connected through Azure Virtual Network gateway (VPN) or ExpressRoute gateway access Azure PaaS Service over VNet service endpoints?
Por predefinição, os recursos de serviço do Azure obtidos para redes virtuais não são acessíveis a partir de redes no local. If you want to allow traffic from on-premises, you must also allow public (typically, NAT) IP addresses from your on-premises or ExpressRoute. These IP addresses can be added through the IP firewall configuration for the Azure service resources.

### <a name="can-i-use-vnet-service-endpoint-feature-to-secure-azure-service-to-multiple-subnets-within-a-virtual-network-or-across-multiple-virtual-networks"></a>Can I use VNet Service Endpoint feature to secure Azure service to multiple subnets within a virtual network or across multiple virtual networks?
To secure Azure services to multiple subnets within a virtual network or across multiple virtual networks, enable service endpoints on the network side on each of the subnets independently and then secure Azure service resources to all of the subnets by setting up appropriate VNet ACLs on the Azure service side.
 
### <a name="how-can-i-filter-outbound-traffic-from-a-virtual-network-to-azure-services-and-still-use-service-endpoints"></a>How can I filter outbound traffic from a virtual network to Azure services and still use service endpoints?
If you want to inspect or filter the traffic destined to an Azure service from a virtual network, you can deploy a network virtual appliance within the virtual network. You can then apply service endpoints to the subnet where the network virtual appliance is deployed and secure Azure service resources only to this subnet through VNet ACLs. This scenario might also be helpful if you wish to restrict Azure service access from your virtual network only to specific Azure resources using network virtual appliance filtering. Para obter mais informações, veja [saída com aplicações de rede virtual](https://docs.microsoft.com/azure/architecture/reference-architectures/dmz/nva-ha).

### <a name="what-happens-when-you-access-an-azure-service-account-that-has-a-virtual-network-access-control-list-acl-enabled-from-outside-the-vnet"></a>What happens when you access an Azure service account that has a virtual network access control list (ACL) enabled from outside the VNet?
The HTTP 403 or HTTP 404 error is returned.

### <a name="are-subnets-of-a-virtual-network-created-in-different-regions-allowed-to-access-an-azure-service-account-in-another-region"></a>Are subnets of a virtual network created in different regions allowed to access an Azure service account in another region? 
Yes, for most of the Azure services, virtual networks created in different regions can access Azure services in another region through the VNet service endpoints. For example, if an Azure Cosmos DB account is in West US or East US and virtual networks are in multiple regions, the virtual network can access Azure Cosmos DB. Storage and SQL are exceptions and are regional in nature and both the virtual network and the Azure service need to be in the same region.
  
### <a name="can-an-azure-service-have-both-a-vnet-acl-and-an-ip-firewall"></a>Can an Azure service have both a VNet ACL and an IP firewall?
Yes, a VNet ACL and an IP firewall can co-exist. Both features complement each other to ensure isolation and security.
 
### <a name="what-happens-if-you-delete-a-virtual-network-or-subnet-that-has-service-endpoint-turned-on-for-azure-service"></a>What happens if you delete a virtual network or subnet that has service endpoint turned on for Azure service?
Deletion of VNets and subnets are independent operations and are supported even when service endpoints are turned on for Azure services. In cases where the Azure services have VNet ACLs set up, for those VNets and subnets, the VNet ACL information associated with that Azure service is disabled when a VNet or subnet that has VNet service endpoint turned on is deleted.
 
### <a name="what-happens-if-an-azure-service-account-that-has-a-vnet-service-endpoint-enabled-is-deleted"></a>What happens if an Azure service account that has a VNet Service endpoint enabled is deleted?
The deletion of an Azure service account is an independent operation and is supported even when the service endpoint is enabled on the network side and VNet ACLs are set up on Azure service side. 

### <a name="what-happens-to-the-source-ip-address-of-a-resource-like-a-vm-in-a-subnet-that-has-vnet-service-endpoint-enabled"></a>What happens to the source IP address of a resource (like a VM in a subnet) that has VNet service endpoint enabled?
When virtual network service endpoints are enabled, the source IP addresses of the resources in your virtual network's subnet switches from using public IPV4 addresses to the Azure virtual network's private IP addresses for traffic to Azure service. Note that this can cause specific IP firewalls that are set to public IPV4 address earlier on the Azure services to fail. 

### <a name="does-the-service-endpoint-route-always-take-precedence"></a>Does the service endpoint route always take precedence?
Service endpoints add a system route which takes precedence over BGP routes and provides optimum routing for the service endpoint traffic. Service endpoints always take service traffic directly from your virtual network to the service on the Microsoft Azure backbone network. For more information about how Azure selects a route, see [Azure Virtual network traffic routing](virtual-networks-udr-overview.md).
 
### <a name="how-does-nsg-on-a-subnet-work-with-service-endpoints"></a>How does NSG on a subnet work with service endpoints?
To reach the Azure service, NSGs need to allow outbound connectivity. If your NSGs are opened to all Internet outbound traffic, then the service endpoint traffic should work. You can also limit the outbound traffic to service IPs only using the Service tags.  
 
### <a name="what-permissions-do-i-need-to-set-up-service-endpoints"></a>What permissions do I need to set up service endpoints?
Service endpoints can be configured on a virtual network independently by a user with write access to the virtual network. To secure Azure service resources to a VNet, the user must have permission **Microsoft.Network/virtualNetworks/subnets/joinViaServiceEndpoint/action** for the subnets being added. This permission is included in the built-in service administrator role by default and can be modified by creating custom roles. Learn more about built-in roles and assigning specific permissions to [custom roles](https://docs.microsoft.com/azure/role-based-access-control/custom-roles?toc=%2fazure%2fvirtual-network%2ftoc.json).
 

### <a name="can-i-filter-virtual-network-traffic-to-azure-services-allowing-only-specific-azure-service-resources-over-vnet-service-endpoints"></a>Can I filter virtual network traffic to Azure services, allowing only specific azure service resources, over VNet service endpoints? 

Virtual network (VNet) service endpoint policies allow you to filter virtual network traffic to Azure services, allowing only specific Azure service resources over the service endpoints. Endpoint policies provide granular access control from the virtual network traffic to the Azure services. You can learn more about the service endpoint policies [here](virtual-network-service-endpoint-policies-overview.md).

### <a name="does-azure-active-directory-azure-ad-support-vnet-service-endpoints"></a>Does Azure Active Directory (Azure AD) support VNet service endpoints?

Azure Active Directory (Azure AD) doesn't support service endpoints natively. Complete list of Azure Services supporting VNet service endpoints can be seen [here](https://docs.microsoft.com/azure/virtual-network/virtual-network-service-endpoints-overview). Note that the "Microsoft.AzureActiveDirectory" tag listed under services supporting service endpoints is used for supporting service endpoints to ADLS Gen 1. For ADLS Gen 1, virtual network integration for Azure Data Lake Storage Gen1 makes use of the virtual network service endpoint security between your virtual network and Azure Active Directory (Azure AD) to generate additional security claims in the access token. Essas afirmações são, posteriormente, utilizadas para autenticar a rede virtual na conta do Data Lake Storage Gen1 e permitir o acesso. Learn more about [Azure Data Lake Store Gen 1 VNet Integration](../data-lake-store/data-lake-store-network-security.md?toc=%2fazure%2fvirtual-network%2ftoc.json)

### <a name="are-there-any-limits-on-how-many-vnet-service-endpoints-i-can-set-up-from-my-vnet"></a>Are there any limits on how many VNet service endpoints I can set up from my VNet?
There is no limit on the total number of VNet service endpoints in a virtual network. For an Azure service resource (such as an Azure Storage account), services may enforce limits on the number of subnets used for securing the resource. The following table shows some example limits: 

|||
|---|---|
|Serviço do Azure| Limits on VNet rules|
|Armazenamento do Azure| 100|
|SQL do Azure| 128|
|Armazém de Dados SQL do Azure|  128|
|Azure KeyVault|    127|
|Azure Cosmos DB|   64|
|Hub de Eventos do Azure|   128|
|Azure Service Bus| 128|
|Azure Data Lake Store V1|  100|
 
>[!NOTE]
> The limits are subjected to changes at the discretion of the Azure service. Refer to the respective service documentation for services details. 




  



