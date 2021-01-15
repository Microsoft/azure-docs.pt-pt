---
title: Rede Virtual do Azure
description: Conheça os conceitos e funcionalidades da Rede Virtual Azure, incluindo espaço de endereço, sub-redes, regiões e subscrições.
services: virtual-network
documentationcenter: na
author: KumudD
Customer intent: As someone with a basic network background that is new to Azure, I want to understand the capabilities of Azure Virtual Network, so that my Azure resources such as VMs, can securely communicate with each other, the internet, and my on-premises resources.
ms.service: virtual-network
ms.devlang: na
ms.topic: overview
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 12/03/2020
ms.author: kumud
ms.openlocfilehash: e1efd02d60ba874178f24da2b441fc122e4a1912
ms.sourcegitcommit: d59abc5bfad604909a107d05c5dc1b9a193214a8
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 01/14/2021
ms.locfileid: "98218539"
---
# <a name="what-is-azure-virtual-network"></a>O que é a Rede Virtual do Azure?

A Azure Virtual Network (VNet) é o bloco de construção fundamental para a sua rede privada em Azure. O VNet permite que muitos tipos de recursos Azure, como as Máquinas Virtuais Azure (VM), comuniquem-se de forma segura entre si, a internet e as redes no local. O VNet é semelhante a uma rede tradicional que operaria no seu próprio centro de dados, mas traz consigo benefícios adicionais da infraestrutura do Azure, como escala, disponibilidade e isolamento.

## <a name="why-use-an-azure-virtual-network"></a>Porquê utilizar uma rede Virtual Azure?
A rede virtual Azure permite que os recursos Azure se comuniquem de forma segura entre si, a internet e as redes no local. Os cenários-chave que pode realizar uma rede virtual incluem - comunicação de recursos Azure com a internet, comunicação entre recursos Azure, comunicação com recursos no local, filtragem do tráfego de rede, tráfego de rede de encaminhamento e integração com os serviços Azure.

### <a name="communicate-with-the-internet"></a>Comunicar com a Internet

Todos os recursos num VNet podem comunicar-se à internet, por padrão. Pode comunicar com um recurso à entrada, ao atribuir-lhe um endereço IP público ou um Balanceador de Carga público. Também pode utilizar o IP público ou o Balanceador de Carga público para gerir as suas ligações de saída.  Para saber mais sobre as ligações de saída no Azure, veja [Ligações de saída](../load-balancer/load-balancer-outbound-connections.md), [Endereços IP públicos](virtual-network-public-ip-address.md) e [Balanceador de Carga](../load-balancer/load-balancer-overview.md).

>[!NOTE]
>Ao utilizar apenas um [Balanceador de Carga Standard](../load-balancer/load-balancer-overview.md) interno, a conectividade de saída não está disponível até definir como pretende que as [ligações de saída](../load-balancer/load-balancer-outbound-connections.md) trabalhem com um IP público de nível de instância ou um Balanceador de Carga público.

### <a name="communicate-between-azure-resources"></a>Comunicação entre os recursos do Azure

Os recursos do Azure comunicam de forma segura entre si, de uma das seguintes formas:

- **Através de uma rede virtual**: pode implementar VMs e vários outros tipos de recursos do Azure numa rede virtual, como Ambientes do Serviço de Aplicações do Azure, Serviço de Kubernetes do Azure (AKS) e Conjuntos de Dimensionamento de Máquinas Virtuais do Azure. Para ver uma lista completa dos recursos do Azure que pode implementar numa rede virtual, veja [Integração de serviço da rede virtual](virtual-network-for-azure-services.md).
- **Através de um ponto final de serviço de rede virtual**: Estenda o espaço de endereço privado da sua rede virtual e a identidade da sua rede virtual para recursos de serviço Azure, tais como contas de Armazenamento Azure e Base de Dados Azure SQL, através de uma ligação direta. Os pontos finais de serviço permitem-lhe obter os seus recursos críticos de serviço do Azure para apenas uma rede virtual. Para obter mais informações, veja [Descrição geral de pontos finais de serviço de rede virtual](virtual-network-service-endpoints-overview.md).
- **Através do VNet Peering:** Pode ligar redes virtuais entre si, permitindo que os recursos em qualquer uma das redes virtuais se comuniquem entre si, utilizando o olhar de rede virtual. As redes virtuais a que liga podem estar nas mesmas regiões ou em regiões diferentes do Azure. Para obter mais informações, veja [Peering de rede virtual](virtual-network-peering-overview.md).

### <a name="communicate-with-on-premises-resources"></a>Comunicação com os recursos no local

Pode ligar os computadores e redes no local a uma rede virtual, com qualquer combinação das seguintes opções:

- **Rede privada virtual (VPN) ponto a site:** estabelecida entre uma rede virtual e um único computador na sua rede. Cada computador que pretende estabelecer conectividade com uma rede virtual tem de configurar a respetiva ligação. Este tipo de ligação é excelente se estiver a começar a utilizar o Azure ou para os programadores, uma vez que necessita de pouca ou nenhumas alterações à sua rede existente. A comunicação entre o computador e uma rede virtual é enviada através de um túnel encriptado através da Internet. Para obter mais informações, veja [VPN ponto a site](../vpn-gateway/point-to-site-about.md?toc=%2fazure%2fvirtual-network%2ftoc.json#).
- **Rede de VPNs:** estabelecida entre o dispositivo VPN no local e um Gateway de VPN do Azure que é implementado numa rede virtual. Este tipo de ligação permite a qualquer recurso no local, que esteja autorizado por si, a aceder a uma rede virtual. A comunicação entre o dispositivo VPN no local e um gateway de VPN do Azure é enviada por um túnel encriptado através da Internet. Para obter mais informações, veja [Rede de VPNs](../vpn-gateway/design.md?toc=%2fazure%2fvirtual-network%2ftoc.json#s2smulti).
- **Azure ExpressRoute:** estabelecida entre a rede e o Azure, através de um parceiro do ExpressRoute. Esta ligação é privada. O tráfego não é transmitido pela Internet. Para saber mais, veja [ExpressRoute](../expressroute/expressroute-introduction.md?toc=%2fazure%2fvirtual-network%2ftoc.json).

### <a name="filter-network-traffic"></a>Filtrar o tráfego de rede

Pode filtrar o tráfego de rede entre as sub-redes com uma ou ambas das seguintes opções:

- **Grupos de segurança de rede:** Os grupos de segurança da rede e os grupos de segurança de aplicações podem conter várias regras de segurança de entrada e saída que lhe permitem filtrar o tráfego de e para os recursos através do endereço IP de origem e destino, porto e protocolo. Para saber mais, consulte [grupos de segurança da Rede](./network-security-groups-overview.md#network-security-groups) ou [grupos de segurança de aplicações.](./network-security-groups-overview.md#application-security-groups)
- **Aplicações virtuais de rede:** uma aplicação virtual de rede é uma VM que executa uma função de rede, como uma firewall, otimização de rede alargada ou outra função de rede. Para ver uma lista das aplicações virtuais de rede disponíveis que pode implementar numa rede virtual, veja [Azure Marketplace](https://azuremarketplace.microsoft.com/marketplace/apps/category/networking?page=1&subcategories=appliances).

### <a name="route-network-traffic"></a>Encaminhar o tráfego de rede

O Azure encaminha tráfego entre sub-redes, redes virtuais ligadas, redes no local e na Internet, por predefinição. Pode implementar uma ou ambas das seguintes opções para substituir as rotas predefinidas que o Azure cria:

- **Tabelas de rotas:** pode criar tabelas de rotas personalizadas com rotas que controlam para onde o tráfego é encaminhado para cada sub-rede. Obtenha mais informações sobre [tabelas de rotas](virtual-networks-udr-overview.md#user-defined).
- **Rotas de protocolo BGP (Border Gateway Protocol):** se ligar a rede virtual à sua rede no local através de uma ligação de Gateway de VPN do Azure ou ExpressRoute, pode propagar as rotas de protocolo BGP no local para as suas redes virtuais. Saiba mais sobre como utilizar o protocolo BGP com o [Gateway de VPN do Azure](../vpn-gateway/vpn-gateway-bgp-overview.md?toc=%2fazure%2fvirtual-network%2ftoc.json) e o [ExpressRoute](../expressroute/expressroute-routing.md?toc=%2fazure%2fvirtual-network%2ftoc.json#dynamic-route-exchange).

### <a name="virtual-network-integration-for-azure-services"></a>Integração de rede virtual para serviços do Azure

A integração dos serviços Azure a uma rede virtual Azure permite o acesso privado ao serviço a partir de máquinas virtuais ou recursos de computação na rede virtual.
Pode integrar os serviços Azure na sua rede virtual com as seguintes opções:
- Implantando [instâncias dedicadas do serviço](virtual-network-for-azure-services.md) numa rede virtual. Os serviços podem então ser acedidos de forma privada dentro da rede virtual e a partir de redes no local.
- Utilizar [o Private Link](../private-link/private-link-overview.md) para aceder a privados a uma instância específica do serviço a partir da sua rede virtual e a partir de redes no local.
- Também pode aceder ao serviço utilizando pontos finais públicos, estendendo uma rede virtual ao serviço, através [de pontos finais de serviço.](virtual-network-service-endpoints-overview.md) Os pontos finais de serviço permitem que os recursos de serviço sejam seguros para a rede virtual.
 

## <a name="azure-vnet-limits"></a>Limites Azure VNet

Existem certos limites em torno do número de recursos Azure que pode implementar. A maioria dos limites de rede Azure estão nos valores máximos. No entanto, pode [aumentar determinados limites de rede](../azure-portal/supportability/networking-quota-requests.md) conforme especificado na página de [limites VNet](../azure-resource-manager/management/azure-subscription-service-limits.md#networking-limits). 

## <a name="pricing"></a>Preços

Não há qualquer custo para a utilização do Azure VNet, é livre de custos. Os encargos padrão são aplicáveis para recursos, tais como Máquinas Virtuais (VMs) e outros produtos. Para saber mais, consulte [os preços da VNet](https://azure.microsoft.com/pricing/details/virtual-network/) e a [calculadora de preços](https://azure.microsoft.com/pricing/calculator/)da Azure.

## <a name="next-steps"></a>Passos seguintes
 - Saiba mais sobre [os conceitos e as melhores práticas da Rede Virtual Azure.](concepts-and-best-practices.md)
 - Para começar a utilizar uma rede virtual, crie uma, implemente algumas VMs na mesma e comunique entre as VMs. Para saber como, veja o início rápido [Criar uma rede virtual](quick-create-portal.md).