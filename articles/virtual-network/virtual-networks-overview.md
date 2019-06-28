---
title: Rede Virtual do Azure | Microsoft Docs
description: Saiba mais sobre os conceitos e as funcionalidades da Rede Virtual do Azure.
services: virtual-network
documentationcenter: na
author: anavinahar
tags: azure-resource-manager
Customer intent: As someone with a basic network background that is new to Azure, I want to understand the capabilities of Azure Virtual Network, so that my Azure resources such as VMs, can securely communicate with each other, the internet, and my on-premises resources.
ms.service: virtual-network
ms.devlang: na
ms.topic: overview
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 06/19/2019
ms.author: anavin
ms.openlocfilehash: 0d6762c8f3034923ddc0fe7dcf0cc2df34bd3629
ms.sourcegitcommit: 08138eab740c12bf68c787062b101a4333292075
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 06/22/2019
ms.locfileid: "67332108"
---
# <a name="what-is-azure-virtual-network"></a>O que é a Rede Virtual do Azure?

Rede Virtual do Azure (VNet) é o bloco de construção fundamental para a sua rede privada no Azure. VNet permite vários tipos de recursos do Azure, tais como máquinas virtuais (VM) do Azure, para comunicar entre si, da internet, de forma segura e redes no local. VNet é semelhante a uma rede tradicional que funcionariam em seu próprio Datacenter, mas traz benefícios adicionais da infraestrutura do Azure, como o dimensionamento, disponibilidade e isolamento.

## <a name="vnet-concepts"></a>Conceitos de VNet

- **Espaço de endereços:** Ao criar uma VNet, tem de especificar um espaço de endereços IP privado personalizado através de endereços (RFC 1918) públicos e privados. O Azure atribui aos recursos numa rede virtual um endereço IP privado a partir do espaço de endereços que atribuir. Por exemplo, se implementar uma VM numa VNet com o espaço de endereços, 10.0.0.0/16, a VM será atribuído um IP privado, como 10.0.0.4.
- **Sub-redes:** Sub-redes permitem-lhe segmentar a rede virtual num ou mais redes secundárias e atribuir uma parte do espaço de endereços da rede virtual para cada sub-rede. Em seguida, pode implementar recursos do Azure numa sub-rede específica. Assim como numa rede tradicional, sub-redes permitem-lhe segmentar o seu espaço de endereços da VNet em segmentos que são adequados para a rede interna da organização. Isso também melhora a eficiência de alocação de endereço. Pode proteger os recursos dentro de sub-redes utilizar grupos de segurança de rede. Para obter mais informações, consulte [grupos de segurança](/security-overview.md).
- **Regiões**: VNet tem um âmbito para uma região/localização única; No entanto, várias redes virtuais a partir de regiões diferentes podem ser ligadas em conjunto com o Peering de rede Virtual.
- **Subscrição:** VNet tem um âmbito a uma subscrição. Pode implementar várias redes virtuais dentro de cada [subscrição](../azure-glossary-cloud-terminology.md?toc=%2fazure%2fvirtual-network%2ftoc.json#subscription) do Azure e [região](../azure-glossary-cloud-terminology.md?toc=%2fazure%2fvirtual-network%2ftoc.json#region) do Azure.

## <a name="best-practices"></a>Melhores práticas

À medida que cria a sua rede no Azure, é importante ter em mente os seguintes princípios de universal design:

- Certifique-se de espaços de endereço não sobrepostos. Certifique-se de que o seu espaço de endereços da VNet (bloco CIDR) não se sobrepõem com a sua organização do outros intervalos de rede.
- As sub-redes não devem abranger o espaço de endereços completo da VNet. Planeie com antecedência e reservar algum espaço de endereço para o futuro.
- Recomenda-se que tiver menos VNets grandes que várias VNets pequeno. Isto irá impedir que a sobrecarga de gerenciamento.
- Proteger a VNet com grupos de segurança de rede (NSGs).

## <a name="communicate-with-the-internet"></a>Comunicar com a Internet

Todos os recursos numa VNet podem comunicar saída à internet, por predefinição. Pode comunicar com um recurso à entrada, ao atribuir-lhe um endereço IP público ou um Balanceador de Carga público. Também pode utilizar o IP público ou o Balanceador de Carga público para gerir as suas ligações de saída.  Para saber mais sobre as ligações de saída no Azure, veja [Ligações de saída](../load-balancer/load-balancer-outbound-connections.md), [Endereços IP públicos](virtual-network-public-ip-address.md) e [Balanceador de Carga](../load-balancer/load-balancer-overview.md).

>[!NOTE]
>Ao utilizar apenas um [Balanceador de Carga Standard](../load-balancer/load-balancer-standard-overview.md) interno, a conectividade de saída não está disponível até definir como pretende que as [ligações de saída](../load-balancer/load-balancer-outbound-connections.md) trabalhem com um IP público de nível de instância ou um Balanceador de Carga público.

## <a name="communicate-between-azure-resources"></a>Comunicar entre os recursos do Azure

Os recursos do Azure comunicam de forma segura entre si, de uma das seguintes formas:

- **Através de uma rede virtual**: Pode implementar VMs e vários outros tipos de recursos do Azure a uma rede virtual, como ambientes de serviço de aplicações do Azure, o Azure Kubernetes Service (AKS) e os conjuntos de dimensionamento de máquinas virtuais do Azure. Para ver uma lista completa dos recursos do Azure que pode implementar numa rede virtual, veja [Integração de serviço da rede virtual](virtual-network-for-azure-services.md).
- **Por meio de um ponto de extremidade do serviço de rede virtual**: Expanda o seu espaço de endereços privados da rede virtual e a identidade da sua rede virtual aos recursos de serviço do Azure, como contas de armazenamento do Azure e bases de dados SQL do Azure, através de uma ligação direta. Os pontos finais de serviço permitem-lhe obter os seus recursos críticos de serviço do Azure para apenas uma rede virtual. Para obter mais informações, veja [Descrição geral de pontos finais de serviço de rede virtual](virtual-network-service-endpoints-overview.md).
- **Através de VNet Peering**: Pode ligar redes virtuais entre si, ativando recursos em qualquer rede virtual para comunicarem entre si, com o peering de rede virtual. As redes virtuais a que liga podem estar nas mesmas regiões ou em regiões diferentes do Azure. Para obter mais informações, veja [Peering de rede virtual](virtual-network-peering-overview.md).

## <a name="communicate-with-on-premises-resources"></a>Comunicar com os recursos no local

Pode ligar os computadores e redes no local a uma rede virtual, com qualquer combinação das seguintes opções:

- **Ponto a site rede privada virtual (VPN):** Estabelecida entre uma rede virtual e um único computador na sua rede. Cada computador que pretende estabelecer conectividade com uma rede virtual tem de configurar a respetiva ligação. Este tipo de ligação é excelente se estiver a começar a utilizar o Azure ou para os programadores, uma vez que necessita de pouca ou nenhumas alterações à sua rede existente. A comunicação entre o computador e uma rede virtual é enviada através de um túnel encriptado através da Internet. Para obter mais informações, veja [VPN ponto a site](../vpn-gateway/vpn-gateway-about-vpngateways.md?toc=%2fazure%2fvirtual-network%2ftoc.json#P2S).
- **VPN de site a site:** Estabelecido entre o dispositivo VPN no local e um Gateway de VPN do Azure que é implementada numa rede virtual. Este tipo de ligação permite a qualquer recurso no local, que esteja autorizado por si, a aceder a uma rede virtual. A comunicação entre o dispositivo VPN no local e um gateway de VPN do Azure é enviada por um túnel encriptado através da Internet. Para obter mais informações, veja [Rede de VPNs](../vpn-gateway/vpn-gateway-about-vpngateways.md?toc=%2fazure%2fvirtual-network%2ftoc.json#s2smulti).
- **O Azure ExpressRoute:** Estabelecida entre a rede e o Azure, através de um parceiro do ExpressRoute. Esta ligação é privada. O tráfego não é transmitido pela Internet. Para saber mais, veja [ExpressRoute](../vpn-gateway/vpn-gateway-about-vpngateways.md?toc=%2fazure%2fvirtual-network%2ftoc.json#ExpressRoute).

## <a name="filter-network-traffic"></a>Filtre o tráfego de rede

Pode filtrar o tráfego de rede entre as sub-redes com uma ou ambas das seguintes opções:

- **Grupos de segurança:** Grupos de segurança de rede e os grupos de segurança de aplicações podem conter várias regras de segurança de entrada e saída que permitem filtrar o tráfego de e para recursos por endereço IP de origem e de destino, porta e protocolo. Para obter mais informações, consulte [grupos de segurança de rede](security-overview.md#network-security-groups) ou [grupos de segurança de aplicativo](security-overview.md#application-security-groups).
- **Aplicações virtuais de rede:** Uma aplicação virtual de rede é uma VM que executa uma função de rede, como uma firewall, otimização de WAN ou outra função de rede. Para ver uma lista das aplicações virtuais de rede disponíveis que pode implementar numa rede virtual, veja [Azure Marketplace](https://azuremarketplace.microsoft.com/marketplace/apps/category/networking?page=1&subcategories=appliances).

## <a name="route-network-traffic"></a>Encaminhe o tráfego de rede

O Azure encaminha tráfego entre sub-redes, redes virtuais ligadas, redes no local e na Internet, por predefinição. Pode implementar uma ou ambas das seguintes opções para substituir as rotas predefinidas que o Azure cria:

- **Tabelas de rotas:** Pode criar tabelas de rotas personalizadas com rotas que controlam onde o tráfego é encaminhado para cada sub-rede. Obtenha mais informações sobre [tabelas de rotas](virtual-networks-udr-overview.md#user-defined).
- **Rotas de protocolo (BGP) de gateway dos limites:** Se ligar a rede virtual à sua rede no local através de uma ligação de Gateway de VPN do Azure ou do ExpressRoute, que pode propagar as rotas do BGP no local para suas redes virtuais. Saiba mais sobre como utilizar o protocolo BGP com o [Gateway de VPN do Azure](../vpn-gateway/vpn-gateway-bgp-overview.md?toc=%2fazure%2fvirtual-network%2ftoc.json) e o [ExpressRoute](../expressroute/expressroute-routing.md?toc=%2fazure%2fvirtual-network%2ftoc.json#dynamic-route-exchange).

## <a name="azure-vnet-limits"></a>Limites de VNet do Azure

Existem determinados limites em todo o número de recursos do Azure, que pode implementar. Limites de rede mais do Azure são os valores máximos. No entanto, pode [aumentar determinados limites de rede](../azure-supportability/networking-quota-requests.md) conforme especificado na [VNet limita página](../azure-subscription-service-limits.md#networking-limits). 

## <a name="pricing"></a>Preços

Não utilização de VNet do Azure sem custos, é gratuita. Tarifas padrão das são aplicáveis para recursos, tais como máquinas virtuais (VMs) e outros produtos. Para obter mais informações, consulte [preços de VNet](https://azure.microsoft.com/pricing/details/virtual-network/) e do Azure [Calculadora de preços](https://azure.microsoft.com/pricing/calculator/).

## <a name="next-steps"></a>Passos Seguintes

 Para começar a utilizar uma rede virtual, crie uma, implemente algumas VMs na mesma e comunique entre as VMs. Para saber como, veja o início rápido [Criar uma rede virtual](quick-create-portal.md).
