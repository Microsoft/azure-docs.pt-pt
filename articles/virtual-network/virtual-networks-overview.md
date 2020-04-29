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
ms.openlocfilehash: 967d391d4ac9a9704688dce9636d9a71b2002549
ms.sourcegitcommit: 58faa9fcbd62f3ac37ff0a65ab9357a01051a64f
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/29/2020
ms.locfileid: "80879365"
---
# <a name="what-is-azure-virtual-network"></a>O que é a Rede Virtual do Azure?

A Rede Virtual Azure (VNet) é o bloco de construção fundamental da sua rede privada em Azure. A VNet permite que muitos tipos de recursos Azure, como as Máquinas Virtuais Azure (VM), se comuniquem de forma segura entre si, a internet e as redes no local. A VNet é semelhante a uma rede tradicional que operaria no seu próprio centro de dados, mas traz consigo benefícios adicionais da infraestrutura do Azure, como escala, disponibilidade e isolamento.

## <a name="vnet-concepts"></a>Conceitos VNet

- **Espaço de endereço:** Ao criar um VNet, deve especificar um espaço de endereçoip personalizado privado utilizando endereços públicos e privados (RFC 1918). O Azure atribui aos recursos numa rede virtual um endereço IP privado a partir do espaço de endereços que atribuir. Por exemplo, se implantar um VM num VNet com espaço de endereço, 10.0.0.0.0/16, o VM será atribuído a um IP privado como 10.0.0.4.
- **Subredes:** As subredes permitem segmentar a rede virtual numa ou mais subredes e alocar uma parte do espaço de endereço da rede virtual a cada subrede. Em seguida, pode implantar recursos Azure numa subnet específica. Tal como numa rede tradicional, as subredes permitem segmentar o seu espaço de endereço VNet em segmentos adequados para a rede interna da organização. Isto também melhora a eficiência da atribuição de endereços. Pode garantir recursos dentro de subredes utilizando grupos de segurança de rede. Para mais informações, consulte [grupos de segurança.](security-overview.md)
- **Regiões**: A VNet é traçada para uma única região/local; no entanto, várias redes virtuais de diferentes regiões podem ser conectadas através do Virtual Network Peering.
- **Subscrição:** A VNet está associada a uma subscrição. Pode implementar várias redes virtuais dentro de cada [subscrição](../azure-glossary-cloud-terminology.md?toc=%2fazure%2fvirtual-network%2ftoc.json#subscription) do Azure e [região](../azure-glossary-cloud-terminology.md?toc=%2fazure%2fvirtual-network%2ftoc.json#region) do Azure.

## <a name="best-practices"></a>Melhores práticas

À medida que constrói a sua rede em Azure, é importante ter em conta os seguintes princípios de design universal:

- Certifique-se de espaços de endereço sem sobreposição. Certifique-se de que o seu espaço de endereço VNet (bloco CIDR) não se sobrepõe às outras gamas de rede da sua organização.
- As suas subredes não devem cobrir todo o espaço de endereço do VNet. Planeie com antecedência e reserve algum espaço de endereço para o futuro.
- Recomenda-se que tenha menos VNets grandes do que vários Pequenos VNets. Isto evitará a gestão.
- Proteja os seus VNet's atribuindo grupos de segurança de rede (NSGs) às subredes abaixo delas.

## <a name="communicate-with-the-internet"></a>Comunicar com a Internet

Todos os recursos de um VNet podem comunicar de saída para a internet, por padrão. Pode comunicar com um recurso à entrada, ao atribuir-lhe um endereço IP público ou um Balanceador de Carga público. Também pode utilizar o IP público ou o Balanceador de Carga público para gerir as suas ligações de saída.  Para saber mais sobre as ligações de saída no Azure, veja [Ligações de saída](../load-balancer/load-balancer-outbound-connections.md), [Endereços IP públicos](virtual-network-public-ip-address.md) e [Balanceador de Carga](../load-balancer/load-balancer-overview.md).

>[!NOTE]
>Ao utilizar apenas um [Balanceador de Carga Standard](../load-balancer/load-balancer-standard-overview.md) interno, a conectividade de saída não está disponível até definir como pretende que as [ligações de saída](../load-balancer/load-balancer-outbound-connections.md) trabalhem com um IP público de nível de instância ou um Balanceador de Carga público.

## <a name="communicate-between-azure-resources"></a>Comunicar entre os recursos do Azure

Os recursos do Azure comunicam de forma segura entre si, de uma das seguintes formas:

- **Através de uma rede virtual**: pode implementar VMs e vários outros tipos de recursos do Azure numa rede virtual, como Ambientes do Serviço de Aplicações do Azure, Serviço de Kubernetes do Azure (AKS) e Conjuntos de Dimensionamento de Máquinas Virtuais do Azure. Para ver uma lista completa dos recursos do Azure que pode implementar numa rede virtual, veja [Integração de serviço da rede virtual](virtual-network-for-azure-services.md).
- **Através de um ponto final de serviço de rede virtual**: expanda o seu espaço de endereços privados da rede virtual e a identidade da sua rede virtual aos recursos do serviço do Azure, como Contas de Armazenamento do Azure e bases de dados SQL do Azure, através de uma ligação direta. Os pontos finais de serviço permitem-lhe obter os seus recursos críticos de serviço do Azure para apenas uma rede virtual. Para obter mais informações, veja [Descrição geral de pontos finais de serviço de rede virtual](virtual-network-service-endpoints-overview.md).
- **Através do VNet Peering:** Pode ligar redes virtuais entre si, permitindo que recursos em qualquer rede virtual se comuniquem entre si, utilizando o peering virtual da rede. As redes virtuais a que liga podem estar nas mesmas regiões ou em regiões diferentes do Azure. Para obter mais informações, veja [Peering de rede virtual](virtual-network-peering-overview.md).

## <a name="communicate-with-on-premises-resources"></a>Comunicar com os recursos no local

Pode ligar os computadores e redes no local a uma rede virtual, com qualquer combinação das seguintes opções:

- **Rede privada virtual (VPN) ponto a site:** estabelecida entre uma rede virtual e um único computador na sua rede. Cada computador que pretende estabelecer conectividade com uma rede virtual tem de configurar a respetiva ligação. Este tipo de ligação é excelente se estiver a começar a utilizar o Azure ou para os programadores, uma vez que necessita de pouca ou nenhumas alterações à sua rede existente. A comunicação entre o computador e uma rede virtual é enviada através de um túnel encriptado através da Internet. Para obter mais informações, veja [VPN ponto a site](../vpn-gateway/vpn-gateway-about-vpngateways.md?toc=%2fazure%2fvirtual-network%2ftoc.json#P2S).
- **Rede de VPNs:** estabelecida entre o dispositivo VPN no local e um Gateway de VPN do Azure que é implementado numa rede virtual. Este tipo de ligação permite a qualquer recurso no local, que esteja autorizado por si, a aceder a uma rede virtual. A comunicação entre o dispositivo VPN no local e um gateway de VPN do Azure é enviada por um túnel encriptado através da Internet. Para obter mais informações, veja [Rede de VPNs](../vpn-gateway/vpn-gateway-about-vpngateways.md?toc=%2fazure%2fvirtual-network%2ftoc.json#s2smulti).
- **Azure ExpressRoute:** estabelecida entre a rede e o Azure, através de um parceiro do ExpressRoute. Esta ligação é privada. O tráfego não é transmitido pela Internet. Para saber mais, veja [ExpressRoute](../vpn-gateway/vpn-gateway-about-vpngateways.md?toc=%2fazure%2fvirtual-network%2ftoc.json#ExpressRoute).

## <a name="filter-network-traffic"></a>Filtre o tráfego de rede

Pode filtrar o tráfego de rede entre as sub-redes com uma ou ambas das seguintes opções:

- **Grupos de segurança:** Grupos de segurança de rede e grupos de segurança de aplicações podem conter múltiplas regras de segurança de entrada e saída que lhe permitem filtrar o tráfego de e para os recursos por endereço IP de origem e destino, porto e protocolo. Para saber mais, consulte [grupos](security-overview.md#network-security-groups) de segurança da Rede ou [grupos de segurança de aplicações](security-overview.md#application-security-groups).
- **Aplicações virtuais de rede:** uma aplicação virtual de rede é uma VM que executa uma função de rede, como uma firewall, otimização de rede alargada ou outra função de rede. Para ver uma lista das aplicações virtuais de rede disponíveis que pode implementar numa rede virtual, veja [Azure Marketplace](https://azuremarketplace.microsoft.com/marketplace/apps/category/networking?page=1&subcategories=appliances).

## <a name="route-network-traffic"></a>Encaminhe o tráfego de rede

O Azure encaminha tráfego entre sub-redes, redes virtuais ligadas, redes no local e na Internet, por predefinição. Pode implementar uma ou ambas das seguintes opções para substituir as rotas predefinidas que o Azure cria:

- **Tabelas de rotas:** pode criar tabelas de rotas personalizadas com rotas que controlam para onde o tráfego é encaminhado para cada sub-rede. Obtenha mais informações sobre [tabelas de rotas](virtual-networks-udr-overview.md#user-defined).
- **Rotas de protocolo BGP (Border Gateway Protocol):** se ligar a rede virtual à sua rede no local através de uma ligação de Gateway de VPN do Azure ou ExpressRoute, pode propagar as rotas de protocolo BGP no local para as suas redes virtuais. Saiba mais sobre como utilizar o protocolo BGP com o [Gateway de VPN do Azure](../vpn-gateway/vpn-gateway-bgp-overview.md?toc=%2fazure%2fvirtual-network%2ftoc.json) e o [ExpressRoute](../expressroute/expressroute-routing.md?toc=%2fazure%2fvirtual-network%2ftoc.json#dynamic-route-exchange).

## <a name="virtual-network-integration-for-azure-services"></a>Integração de rede virtual para serviços do Azure

A integração dos serviços Azure a uma rede virtual Azure permite o acesso privado ao serviço a partir de máquinas virtuais ou recursos de computação na rede virtual.
Pode integrar os serviços Azure na sua rede virtual com as seguintes opções:
- Implantação [de instâncias dedicadas do serviço](virtual-network-for-azure-services.md) numa rede virtual. Os serviços podem então ser acedidos a privados dentro da rede virtual e a partir de redes no local.
- Utilizar o [Private Link](../private-link/private-link-overview.md) para aceder a uma instância específica do serviço a partir da sua rede virtual e das redes no local.
- Também pode aceder ao serviço utilizando pontos finais públicos, alargando uma rede virtual ao serviço, através de [pontos finais](virtual-network-service-endpoints-overview.md)de serviço. Os pontos finais do serviço permitem que os recursos de serviço sejam assegurados à rede virtual.
 

## <a name="azure-vnet-limits"></a>Limites Azure VNet

Existem certos limites em torno do número de recursos Azure que pode utilizar. A maioria dos limites de rede Azure estão nos valores máximos. No entanto, pode [aumentar determinados limites](../azure-portal/supportability/networking-quota-requests.md) de rede conforme especificado na página de [limites VNet](../azure-resource-manager/management/azure-subscription-service-limits.md#networking-limits). 

## <a name="pricing"></a>Preços

Não há nenhuma taxa para a utilização do Azure VNet, é livre de custos. Os custos padrão são aplicáveis aos recursos, tais como Máquinas Virtuais (VMs) e outros produtos. Para saber mais, consulte [os preços da VNet](https://azure.microsoft.com/pricing/details/virtual-network/) e a [calculadora](https://azure.microsoft.com/pricing/calculator/)de preços Azure.

## <a name="next-steps"></a>Passos seguintes

 Para começar a utilizar uma rede virtual, crie uma, implemente algumas VMs na mesma e comunique entre as VMs. Para saber como, veja o início rápido [Criar uma rede virtual](quick-create-portal.md).
