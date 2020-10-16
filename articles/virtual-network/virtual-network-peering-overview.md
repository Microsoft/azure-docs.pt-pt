---
title: Peering de Rede Virtual do Azure
titlesuffix: Azure Virtual Network
description: Saiba mais sobre o espreitamento de rede virtual no Azure, incluindo como lhe permite ligar redes na Rede Virtual Azure.
services: virtual-network
documentationcenter: na
author: altambaw
ms.service: virtual-network
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 11/15/2019
ms.author: kumud
ms.openlocfilehash: 61617777efa8241c93b2b5ffe42fc1d914b6ebcf
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/09/2020
ms.locfileid: "91531875"
---
# <a name="virtual-network-peering"></a>Peering de rede virtual

O espreitamento de rede virtual permite-lhe ligar perfeitamente duas ou mais [Redes Virtuais](virtual-networks-overview.md) em Azure. As redes virtuais aparecem como uma para fins de conectividade. O tráfego entre máquinas virtuais em redes virtuais espreitadas utiliza a infraestrutura da espinha dorsal da Microsoft. Tal como o tráfego entre máquinas virtuais na mesma rede, o tráfego é encaminhado apenas através da rede *privada* da Microsoft.

O Azure suporta os seguintes tipos de espreguiçadamento:

* Observação de rede virtual: Ligue redes virtuais dentro da mesma região do Azure.
* Observação global da rede virtual: Ligação de redes virtuais em regiões de Azure.

As vantagens da utilização do peering de redes virtuais, sejam locais ou globais, incluem:

* Uma ligação de baixa latência e largura de banda alta entre os recursos em redes virtuais diferentes.
* A capacidade de recursos numa rede virtual comunicarem com recursos numa rede virtual diferente.
* A capacidade de transferir dados entre redes virtuais através de subscrições Azure, inquilinos do Azure Ative Directory, modelos de implantação e regiões Azure.
* A capacidade de espreitar redes virtuais criadas através do Azure Resource Manager.
* A capacidade de espreitar uma rede virtual criada através do Resource Manager para uma criada através do modelo de implementação clássico. Para saber mais sobre os modelos de implementação do Azure, veja [Understand Azure deployment models](../azure-resource-manager/management/deployment-models.md?toc=%2fazure%2fvirtual-network%2ftoc.json) (Compreender os modelos de implementação do Azure).
* Sem períodos de indisponibilidade para recursos em qualquer rede virtual durante ou após a criação do peering.

O tráfego de rede entre redes virtuais em modo de peering é privado. O tráfego entre as redes virtuais é mantido na rede principal da Microsoft. Não é necessária Internet pública, gateways ou encriptação na comunicação entre as redes virtuais.

## <a name="connectivity"></a>Conectividade

Para redes virtuais espreitadas, os recursos em qualquer uma das redes virtuais podem conectar-se diretamente com recursos na rede virtual espreitada.

A latência de rede entre máquinas virtuais em redes virtuais no modo de peering na mesma região é igual à latência numa rede virtual individual. O débito de rede baseia-se na largura de banda que é permitida para a máquina virtual proporcionalmente ao respetivo tamanho. Não existe qualquer restrição adicional na largura de banda no peering.

O tráfego entre as máquinas virtuais nas redes virtuais em modo de peering é encaminhado diretamente através da infraestrutura principal da Microsoft e não através de um gateway ou numa Internet pública.

Pode aplicar grupos de segurança de rede em qualquer rede virtual para bloquear o acesso a outras redes virtuais ou sub-redes.
Ao configurar o olhar de rede virtual, abra ou feche as regras do grupo de segurança de rede entre as redes virtuais. Se abrir a conectividade total entre redes virtuais, pode aplicar grupos de segurança de rede para bloquear ou negar acesso específico. A conectividade completa é a opção padrão. Para saber mais sobre grupos de segurança de rede, consulte [os grupos de segurança.](security-overview.md)

## <a name="service-chaining"></a>Encadeamento de serviços

O acorrentado de serviço permite-lhe direcionar o tráfego de uma rede virtual para um aparelho virtual ou gateway numa rede espreitada através de rotas definidas pelo utilizador.

Para permitir o acorrentamento de serviços, configurar rotas definidas pelo utilizador que apontam para máquinas virtuais em redes virtuais espreitadas como o próximo endereço IP *de lúpulo.* As rotas definidas pelo utilizador também podem apontar para gateways de rede virtuais para permitir a corrente de serviço.

Pode implementar redes de hub e spoke, onde a rede virtual do hub acolhe componentes de infraestrutura, como um aparelho virtual de rede ou gateway VPN. Todas as redes virtuais “spoke” podem, então, configurar o peering com a rede virtual do concentrador. O tráfego flui através de aparelhos virtuais de rede ou gateways VPN na rede virtual do hub.

O peering de rede virtual permite que o próximo salto numa rota definida pelo utilizador seja o endereço IP de uma máquina virtual na rede virtual em modo de peering ou um gateway VPN. Não é possível fazer uma rota entre redes virtuais com uma rota definida pelo utilizador que especifique um gateway Azure ExpressRoute como o próximo tipo de lúpulo. Para saber mais sobre as rotas definidas pelo utilizador, veja [Descrição geral das rotas definidas pelo utilizador](virtual-networks-udr-overview.md#user-defined). Para aprender a criar um hub e a topologia da rede falada, consulte [a topologia da rede falada por Hub em Azure.](/azure/architecture/reference-architectures/hybrid-networking/hub-spoke?toc=%2fazure%2fvirtual-network%2ftoc.json)

## <a name="gateways-and-on-premises-connectivity"></a>Gateways e conetividade no local

Cada rede virtual, incluindo uma rede virtual espreitada, pode ter o seu próprio portal. Uma rede virtual pode usar a sua porta de entrada para se ligar a uma rede no local. Também pode configurar [ligações virtuais de rede para rede virtual utilizando gateways,](../vpn-gateway/vpn-gateway-vnet-vnet-rm-ps.md?toc=%2fazure%2fvirtual-network%2ftoc.json) mesmo para redes virtuais com vista.

Ao configurar ambas as opções para a interconectividade da rede virtual, o tráfego entre as redes virtuais flui através da configuração de peering. O trânsito usa a espinha dorsal do Azure.

Também pode configurar o gateway na rede virtual espreitada como um ponto de trânsito para uma rede no local. Neste caso, a rede virtual que está a usar um gateway remoto não pode ter o seu próprio portal. Uma rede virtual tem apenas uma porta de entrada. O gateway é um gateway local ou remoto na rede virtual espreguidado, como mostra o seguinte diagrama:

![trânsito de peering de rede virtual](./media/virtual-networks-peering-overview/local-or-remote-gateway-in-peered-virual-network.png)

Tanto o espreitamento da rede virtual como a rede virtual global que espreita o trânsito de gateway.

O trânsito de gateway entre redes virtuais criados através de diferentes modelos de implementação é suportado. O gateway deve estar na rede virtual no modelo Gestor de Recursos. Para saber mais sobre como utilizar um gateway para trânsito, veja [Configurar um gateway de VPN para trânsito num peering de rede virtual](../vpn-gateway/vpn-gateway-peering-gateway-transit.md?toc=%2fazure%2fvirtual-network%2ftoc.json).

Quando se percorre redes virtuais que partilham uma única ligação Azure ExpressRoute, o tráfego entre eles passa pela relação de espreitar. O tráfego usa a rede de espinha dorsal Azure. Pode continuar a utilizar gateways locais em cada rede virtual para ligar ao circuito no local. Caso contrário, pode utilizar um gateway partilhado e configurar o trânsito para a conectividade no local.

## <a name="troubleshoot"></a>Resolução de problemas

Para confirmar que as redes virtuais são espreitadas, pode verificar rotas eficazes. Verifique as rotas de uma interface de rede em qualquer sub-rede de uma rede virtual. Se um peering de rede virtual existe, todas as sub-redes na rede virtual têm rotas com o tipo de salto seguinte *VNet peering*, para cada espaço de endereços em cada virtual rede peered. Para obter mais informações, consulte [diagnosticar um problema de encaminhamento de máquinas virtuais](diagnose-network-routing-problem.md).

Também pode resolver a conectividade com uma máquina virtual numa rede virtual com vista a utilizar o Azure Network Watcher. Uma verificação de conectividade permite-lhe ver como o tráfego é encaminhado da interface de rede de uma máquina virtual de origem para a interface de rede de uma máquina virtual de destino. Para obter mais informações, consulte [as ligações de resolução de problemas com o Observador de Redes Azure utilizando o portal Azure](../network-watcher/network-watcher-connectivity-portal.md#check-connectivity-to-a-virtual-machine).

Também pode experimentar os [problemas de observação da rede virtual Troubleshoot](virtual-network-troubleshoot-peering-issues.md).

## <a name="constraints-for-peered-virtual-networks"></a>Constrangimentos para redes virtuais espreitadas<a name="requirements-and-constraints"></a>

Os seguintes constrangimentos só se aplicam quando as redes virtuais são globalmente espreitadas:

* Os recursos de uma rede virtual não podem comunicar com o endereço IP frontal de um Balancer de Carga Interna Básica (ILB) numa rede virtual globalmente espreitada.
* Alguns serviços que usam um balanceador de carga básico não funcionam sobre o olhar global da rede virtual. Para obter mais informações, veja [quais são os constrangimentos relacionados com os esquilibradores e equilíbrios globais de VNet?](virtual-networks-faq.md#what-are-the-constraints-related-to-global-vnet-peering-and-load-balancers)

Para mais informações, consulte [Requisitos e constrangimentos.](virtual-network-manage-peering.md#requirements-and-constraints) Para saber mais sobre o número suportado de espreitamentos, consulte os [limites de networking](../azure-resource-manager/management/azure-subscription-service-limits.md?toc=%2fazure%2fvirtual-network%2ftoc.json#azure-resource-manager-virtual-networking-limits).

## <a name="permissions"></a>Permissões

Para conhecer as permissões necessárias para criar uma rede virtual de observação, consulte [Permissões](virtual-network-manage-peering.md#permissions).

## <a name="pricing"></a>Preços

Há uma carga nominal para o tráfego de entradas e saídas que usa uma ligação de observação de rede virtual. Para obter mais informações, consulte [os preços da Rede Virtual.](https://azure.microsoft.com/pricing/details/virtual-network)

Gateway Transit é uma propriedade que permite a uma rede virtual utilizar uma porta de entrada VPN/ExpressRoute numa rede virtual espreitada. O trânsito gateway funciona tanto para instalações cruzadas como para a conectividade rede-rede. O tráfego para o gateway (entrada ou saída) na rede virtual esprevada incorre em taxas de observação de rede virtuais no VNet falado (ou VNet não de gateway). Para obter mais informações, consulte [os preços da VPN Gateway](https://azure.microsoft.com/pricing/details/vpn-gateway/) para as tarifas de gateway VPN e os preços do Gateway ExpressRoute para taxas de gateway ExpressRoute.

>[!NOTE]
> Uma versão anterior deste documento declarou que as taxas de observação da rede virtual não seriam aplicadas no VNet falado (ou VNet não de gateway) com o Gateway Transit. Reflete agora preços precisos na página de preços.

## <a name="next-steps"></a>Passos seguintes

* Pode criar um espreitamento entre duas redes virtuais. As redes podem pertencer à mesma subscrição, diferentes modelos de implementação na mesma subscrição ou subscrições diferentes. Conclua um tutorial para um dos cenários seguintes:

    |Modelo de implementação do Azure             | Subscrição  |
    |---------                          |---------|
    |Ambas com Resource Manager              |[Mesma](tutorial-connect-virtual-networks-portal.md)|
    |                                   |[Diferente](create-peering-different-subscriptions.md)|
    |Uma com Resource Manager, outra com clássica  |[Mesma](create-peering-different-deployment-models.md)|
    |                                   |[Diferente](create-peering-different-deployment-models-subscriptions.md)|

* Para aprender a criar um hub e a topologia da rede falada, consulte [a topologia da rede falada por Hub em Azure.](/azure/architecture/reference-architectures/hybrid-networking/hub-spoke?toc=%2fazure%2fvirtual-network%2ftoc.json)
* Para saber sobre todas as definições de observação de rede virtual, consulte [Criar, alterar ou eliminar uma rede virtual que espreita.](virtual-network-manage-peering.md)
* Para obter respostas para o espremiamento de rede virtual comum e perguntas de observação de redes virtuais globais, consulte [VNet Peering](virtual-networks-faq.md#vnet-peering).
