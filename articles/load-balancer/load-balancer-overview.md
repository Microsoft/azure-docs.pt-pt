---
title: O que é o Balanceador de Carga do Azure?
titleSuffix: Azure Load Balancer
description: Descrição geral das funcionalidades, da arquitetura e da implementação do Balanceador de Carga do Azure. Saiba como o Balanceador de Carga funciona e como tirar partido do mesmo na cloud.
services: load-balancer
documentationcenter: na
author: asudbring
ms.service: load-balancer
Customer intent: As an IT administrator, I want to learn more about the Azure Load Balancer service and what I can use it for.
ms.devlang: na
ms.topic: overview
ms.custom: seodec18
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 11/08/2019
ms.author: allensu
ms.openlocfilehash: 108dab4bbdec4e821270565860095187eaceb788
ms.sourcegitcommit: d6b68b907e5158b451239e4c09bb55eccb5fef89
ms.translationtype: HT
ms.contentlocale: pt-PT
ms.lasthandoff: 11/20/2019
ms.locfileid: "74214963"
---
# <a name="what-is-azure-load-balancer"></a>O que é o Balanceador de Carga do Azure?

Com o Balanceador de Carga do Azure, pode dimensionar as aplicações e criar elevada disponibilidade para os seus serviços. O Balanceador de Carga suporta cenários de entrada e de saída, oferece baixa latência e alto débito e dimensiona-se até milhões de fluxos para todas as aplicações TCP e UDP.

Load Balancer distributes new inbound flows that arrive at the Load Balancer's front end to back-end pool instances, according to specified rules and health probes.

A public Load Balancer can provide outbound connections for virtual machines (VMs) inside your virtual network by translating their private IP addresses to public IP addresses.

Azure Load Balancer is available in two pricing tiers or *SKUs*: Basic and Standard. Há diferenças em termos de dimensionamento, funcionalidades e preços. Any scenario that's possible with Basic Load Balancer can also be created with Standard Load Balancer, although the approaches differ slightly. As you learn about Load Balancer, familiarize yourself with the fundamentals and the SKU-specific differences.

## <a name="why-use-load-balancer"></a>Porquê utilizar o Balanceador de Carga

Pode utilizar o Balanceador de Carga do Azure para:

* Load balance incoming internet traffic to your VMs. This configuration is known as a [public Load Balancer](#publicloadbalancer).
* Load balance traffic across VMs inside a virtual network. Também pode alcançar um front-end do Balanceador de Carga a partir de uma rede no local num cenário híbrido. Both of these scenarios use a configuration that is known as an [internal Load Balancer](#internalloadbalancer).
* Encaminhar o tráfego de uma porta para uma porta específica em determinadas VMs com regras de tradução de endereços de rede (NAT).
* Utilize um balanceador de carga público para fornecer [conectividade de saída](load-balancer-outbound-connections.md) às VMs dentro da sua rede virtual.

>[!NOTE]
> O Azure oferece um conjunto de soluções de balanceamento de carga totalmente geridas para os seus cenários. If you're looking for Transport Layer Security (TLS) protocol termination ("SSL offload") or per-HTTP/HTTPS request, application-layer processing, see [What is Azure Application Gateway](../application-gateway/application-gateway-introduction.md). If you're looking for global DNS load balancing, see [What is Traffic Manager](../traffic-manager/traffic-manager-overview.md). Your end-to-end scenarios may benefit from combining these solutions.

## <a name="what-are-load-balancer-resources"></a>O que são os recursos do Balanceador de Carga?

Load Balancer resources are objects specifying how Azure should program its multi-tenant infrastructure to achieve the scenario that you want to create. There's no direct relationship between Load Balancer resources and actual infrastructure. A criação de um balanceador de carga não cria uma instância e a capacidade está sempre disponível.

A Load Balancer resource can be either a public Load Balancer or an internal Load Balancer. The Load Balancer resource's functions are defined by a front end, a rule, a health probe, and a back-end pool definition. You place VMs in the back-end pool by specifying the back-end pool from the VM.

## <a name="fundamental-load-balancer-features"></a>Funcionalidades básicas do Balanceador de Carga

O Balanceador de Carga proporciona as seguintes capacidades básicas para as aplicações TCP e UDP:

* **Balanceamento de carga**

  With Azure Load Balancer, you can create a load-balancing rule to distribute traffic that arrives at the front end to back-end pool instances. Load Balancer uses a hashing algorithm for distribution of inbound flows and rewrites the headers of flows to back-end pool instances. A server is available to receive new flows when a health probe indicates a healthy back-end endpoint.

  By default, Load Balancer uses a 5-tuple hash. The hash includes source IP address, source port, destination IP address, destination port, and IP protocol number to map flows to available servers. You can create affinity to a source IP address by using a 2- or 3-tuple hash for a given rule. Todos os pacotes do mesmo fluxo de pacotes são entregues na mesma instância, atrás do front-end com carga balanceada. When the client initiates a new flow from the same source IP, the source port is changed. As a result, the 5-tuple hash might cause the traffic to go to a different back-end endpoint.

  For more information, see [Configure the distribution mode for Azure Load Balancer](load-balancer-distribution-mode.md). A imagem seguinte apresenta a distribuição baseada em hashes:

  ![Distribuição baseada em hashes](./media/load-balancer-overview/load-balancer-distribution.png)

  *Figura: distribuição baseada em hashes*

* **Encaminhamento de portas**

  With Load Balancer, you can create an inbound NAT rule. This NAT rule forwards traffic from a specific port of a specific front-end IP address to a specific port of a specific back-end instance inside the virtual network. This forwarding is done by the same hash-based distribution as load balancing. Common scenarios for this capability are Remote Desktop Protocol (RDP) or Secure Shell (SSH) sessions to individual VM instances inside an Azure Virtual Network.
  
  You can map multiple internal endpoints to ports on the same front-end IP address. You can use the front-end IP addresses to remotely administer your VMs without an additional jump box.

* **Application independence and transparency**

  Load Balancer doesn't directly interact with TCP or UDP or the application layer. Any TCP or UDP application scenario can be supported. Load Balancer doesn't terminate or originate flows, interact with the payload of the flow, or provide any application layer gateway function. Protocol handshakes always occur directly between the client and the back-end pool instance. Uma resposta a um fluxo de entrada é sempre uma resposta de uma máquina virtual. Quando o fluxo chega à máquina virtual, o endereço IP de origem original também é preservado.

  * Cada ponto final só recebe uma resposta de uma VM. For example, a TCP handshake always occurs between the client and the selected back-end VM. A response to a request to a front end is a response generated by a back-end VM. When you successfully validate connectivity to a front end, you're validating the end-to-end connectivity to at least one back-end virtual machine.
  * Application payloads are transparent to Load Balancer. Any UDP or TCP application can be supported. For workloads that require per HTTP request processing or manipulation of application layer payloads, like parsing of HTTP URLs, you should use a layer 7 load balancer like [Application Gateway](https://azure.microsoft.com/services/application-gateway).
  * Because Load Balancer doesn't interact with the TCP payload and provide TLS offload, you can build end-to-end encrypted scenarios. Using Load Balancer gains large scale-out for TLS applications by terminating the TLS connection on the VM itself. For example, your TLS session keying capacity is only limited by the type and number of VMs you add to the back-end pool. If you require SSL offloading, application layer treatment, or want to delegate certificate management to Azure, use Azure's layer 7 load balancer [Application Gateway](https://azure.microsoft.com/services/application-gateway) instead.

* **Reconfiguração automática**

  Quando aumenta ou reduz instâncias verticalmente, o Balanceador de Carga reconfigura-se a si próprio instantaneamente. Adding or removing VMs from the back-end pool reconfigures the Load Balancer without additional operations on the Load Balancer resource.

* **Sondas de estado de funcionamento**

  To determine the health of instances in the back-end pool, Load Balancer uses health probes that you define. Quando uma sonda não consegue responder, o Balanceador de Carga deixa de enviar ligações novas para as instâncias em mau estado de funcionamento. A probe failure doesn't affect existing connections. The connection continues until the application terminates the flow, an idle timeout occurs, or the VM shuts down.

  Load Balancer provides different health probe types for TCP, HTTP, and HTTPS endpoints. For more information, see [Probe types](load-balancer-custom-probe-overview.md#types).

  When using Classic cloud services, an additional type is allowed: [Guest agent](load-balancer-custom-probe-overview.md#guestagent). A guest agent should be considered to be a health probe of last resort. Microsoft doesn't recommend them if other options are available.

* **Ligações de saída (SNAT)**

  All outbound flows from private IP addresses inside your virtual network to public IP addresses on the internet can be translated to a front-end IP address of the Load Balancer. When a public front end is tied to a back-end VM by way of a load-balancing rule, Azure translates outbound connections to the public front-end IP address. This configuration has the following advantages:

  * Easy upgrade and disaster recovery of services, because the front end can be dynamically mapped to another instance of the service.
  * Easier access control list (ACL) management. ACLs expressed as front-end IPs don't change as services scale up or down or get redeployed. Translating outbound connections to a smaller number of IP addresses than machines reduces the burden of implementing safe recipient lists.

  For more information, see [Outbound connections in Azure](load-balancer-outbound-connections.md).

Standard Load Balancer has additional SKU-specific capabilities beyond these fundamentals, as described below.

## <a name="skus"></a> Comparação de SKUs do Balanceador de Carga

Load Balancer supports both Basic and Standard SKUs. These SKUs differ in scenario scale, features, and pricing. Any scenario that's possible with Basic Load Balancer can be created with Standard Load Balancer. The APIs for both SKUs are similar and are invoked through the specification of a SKU. The API for supporting SKUs for Load Balancer and the public IP is available starting with the `2017-08-01` API. Both SKUs share the same general API and structure.

The complete scenario configuration might differ slightly depending on SKU. A documentação do Balanceador de Carga indica se um artigo se aplica apenas a um SKU específico. Para comparar e compreender as diferenças, veja a tabela seguinte. For more information, see [Azure Standard Load Balancer overview](load-balancer-standard-overview.md).

>[!NOTE]
> Os designs novos devem adotar o Balanceador de Carga Standard.

As VMs autónomas, os conjuntos de disponibilidade e os conjuntos de dimensionamento de máquinas virtuais só podem ser ligados a um SKU, nunca a ambos. Load Balancer and the public IP address SKU must match when you use them with public IP addresses. Load Balancer and public IP SKUs aren't mutable.

The best practice is to specify the SKUs explicitly. Nesta fase, estamos a manter as alterações obrigatórias no mínimo. If a SKU isn't specified, the default is the `2017-08-01` API version of the Basic SKU.

>[!IMPORTANT]
>Standard Load Balancer is a new Load Balancer product. It is largely a superset of Basic Load Balancer, but there are important differences between the two products. Qualquer cenário completo possível no Balanceador de Carga Básico também pode ser criado com o Balanceador de Carga Standard. If you're already used to Basic Load Balancer, compare it with Standard Load Balancer to understand the latest changes in their behavior.

[!INCLUDE [comparison table](../../includes/load-balancer-comparison-table.md)]

For more information, see [Load balancer limits](https://aka.ms/lblimits). Relativamente aos detalhes do Balanceador de Carga Standard, veja a [descrição geral](load-balancer-standard-overview.md), os [preços](https://aka.ms/lbpricing) e o [SLA](https://aka.ms/lbsla).

## <a name="concepts"></a>Conceitos

### <a name = "publicloadbalancer"></a>Balanceador de Carga Público

A public Load Balancer maps the public IP address and port of incoming traffic to the private IP address and port of the VM. Load Balancer maps traffic the other way around for the response traffic from the VM. You can distribute specific types of traffic across multiple VMs or services by applying load-balancing rules. Por exemplo, pode distribuir a carga do tráfego de pedidos da Web entre múltiplos servidores Web.

>[!NOTE]
>You can implement only one public Load Balancer and one internal Load Balancer per availability set.

The following figure shows a load-balanced endpoint for web traffic that is shared among three VMs for the public and TCP port 80. Estas três VMs estão num conjunto com carga balanceada.

![Exemplo de balanceador de carga público](./media/load-balancer-overview/IC727496.png)

*Figure: Balancing web traffic by using a public Load Balancer*

Internet clients send webpage requests to the public IP address of a web app on TCP port 80. Azure Load Balancer distributes the requests across the three VMs in the load-balanced set. For more information about Load Balancer algorithms, see [Fundamental Load Balancer features](load-balancer-overview.md##fundamental-load-balancer-features).

Azure Load Balancer distributes network traffic equally among multiple VM instances by default. Também pode configurar a afinidade de sessão. For more information, see [Configure the distribution mode for Azure Load Balancer](load-balancer-distribution-mode.md).

### <a name = "internalloadbalancer"></a> Balanceador de Carga Interno

An internal Load Balancer directs traffic only to resources that are inside a virtual network or that use a VPN to access Azure infrastructure, in contrast to a public Load Balancer. Azure infrastructure restricts access to the load-balanced front-end IP addresses of a virtual network. Front-end IP addresses and virtual networks are never directly exposed to an internet endpoint. As aplicações de linha de negócio internas são executadas no Azure e acedidas de dentro do Azure ou a partir de recursos no local.

Os balanceadores de carga internos permitem os seguintes tipos de balanceador de carga:

* **Within a virtual network**: Load balancing from VMs in the virtual network to a set of VMs that are in the same virtual network.
* **For a cross-premises virtual network**: Load balancing from on-premises computers to a set of VMs that are in the same virtual network.
* **For multi-tier applications**: Load balancing for internet-facing multi-tier applications where the back-end tiers aren't internet-facing. The back-end tiers require traffic load balancing from the internet-facing tier. See the next figure.
* **Para aplicações de linha de negócio**: balanceamento de carga para aplicações de linha de negócio que estão alojadas no Azure sem hardware ou software adicional de balanceador de carga. This scenario includes on-premises servers that are in the set of computers whose traffic is load balanced.

![Exemplo de Balanceador de Carga Interno](./media/load-balancer-overview/IC744147.png)

*Figure: Balancing multi-tier applications by using both public and internal Load Balancer*

## <a name="pricing"></a>Preços

Standard Load Balancer usage is charged.

* Number of configured load-balancing and outbound rules. Inbound NAT rules don't count in the total number of rules.
* Amount of data processed inbound and outbound independent of rules.

For Standard Load Balancer pricing information, see [Load Balancer pricing](https://azure.microsoft.com/pricing/details/load-balancer/).

O Balanceador de Carga Básico é disponibilizado sem custos.

## <a name="sla"></a>SLA

For information about the Standard Load Balancer SLA, see [SLA for Load Balancer](https://aka.ms/lbsla).

## <a name="limitations"></a>Limitações

* Load Balancer provides load balancing and port forwarding for specific TCP or UDP protocols. Load-balancing rules and inbound NAT rules support TCP and UDP, but not other IP protocols including ICMP.

  Load Balancer doesn't terminate, respond, or otherwise interact with the payload of a UDP or TCP flow. It's not a proxy. Successful validation of connectivity to a front end must take place in-band with the same protocol used in a load balancing or inbound NAT rule. At least one of your virtual machines must generate a response for a client to see a response from a front end.

  Not receiving an in-band response from the Load Balancer front end indicates no virtual machines could respond. Nothing can interact with a Load Balancer front end without a virtual machine able to respond. This principle also applies to outbound connections where port masquerade SNAT is only supported for TCP and UDP. Any other IP protocols, including ICMP, fail. Assign an instance-level public IP address to mitigate this issue. For more information, see [Understanding SNAT and PAT](load-balancer-outbound-connections.md#snat).

* Internal Load Balancers don't translate outbound originated connections to the front end of an internal Load Balancer because both are in private IP address space. Public Load Balancers provide [outbound connections](load-balancer-outbound-connections.md) from private IP addresses inside the virtual network to public IP addresses. For internal Load Balancers, this approach avoids potential SNAT port exhaustion inside a unique internal IP address space, where translation isn't required.

  A side effect is that if an outbound flow from a VM in the back-end pool attempts a flow to front end of the internal Load Balancer in its pool _and_ is mapped back to itself, the two legs of the flow don't match. Because they don't match, the flow fails. The flow succeeds if the flow didn't map back to the same VM in the back-end pool that created the flow to the front end.

  When the flow maps back to itself, the outbound flow appears to originate from the VM to the front end and the corresponding inbound flow appears to originate from the VM to itself. From the guest operating system's point of view, the inbound and outbound parts of the same flow don't match inside the virtual machine. The TCP stack won't recognize these halves of the same flow as being part of the same flow. The source and destination don't match. When the flow maps to any other VM in the back-end pool, the halves of the flow do match and the VM can respond to the flow.

  The symptom for this scenario is intermittent connection timeouts when the flow returns to the same back end that originated the flow. Common workarounds include insertion of a proxy layer behind the internal Load Balancer and using Direct Server Return (DSR) style rules. For more information, see [Multiple Front ends for Azure Load Balancer](load-balancer-multivip-overview.md).

  You can combine an internal Load Balancer with any third-party proxy or use internal [Application Gateway](../application-gateway/application-gateway-introduction.md) for proxy scenarios with HTTP/HTTPS. While you could use a public Load Balancer to mitigate this issue, the resulting scenario is prone to [SNAT exhaustion](load-balancer-outbound-connections.md#snat). Avoid this second approach unless carefully managed.

* In general, forwarding IP fragments isn't supported on load-balancing rules. IP fragmentation of UDP and TCP packets isn't supported on load-balancing rules. High availability ports load-balancing rules can be used to forward existing IP fragments. For more information, see [High availability ports overview](load-balancer-ha-ports-overview.md).

## <a name="next-steps"></a>Passos seguintes

See [Create a Basic Load Balancer](quickstart-create-basic-load-balancer-portal.md) to get started with using a Load Balancer: create one, create VMs with a custom IIS extension installed, and load balance the web app between the VMs.
