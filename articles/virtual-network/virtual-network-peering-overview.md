---
title: Peering da Rede Virtual Azure
titlesuffix: Azure Virtual Network
description: Saiba mais sobre o peering de rede virtual no Azure.
services: virtual-network
documentationcenter: na
author: anavinahar
ms.service: virtual-network
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 11/15/2019
ms.author: anavin
ms.openlocfilehash: 5fb54e812e72b9393ffdf632085d0f32ab8b1988
ms.sourcegitcommit: 7b25c9981b52c385af77feb022825c1be6ff55bf
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/13/2020
ms.locfileid: "79279549"
---
# <a name="virtual-network-peering"></a>Peering de rede virtual

O peering de rede virtual permite-lhe conectar redes na [Rede Virtual Azure.](virtual-networks-overview.md) As redes virtuais aparecem como uma para fins de conectividade. O tráfego entre máquinas virtuais utiliza a infraestrutura da espinha dorsal da Microsoft. Tal como o tráfego entre máquinas virtuais na mesma rede, o tráfego é encaminhado apenas através da rede *privada* da Microsoft.

O Azure suporta os seguintes tipos de observação:

* Peering de rede virtual: Conecte redes virtuais dentro da mesma região azure.
* Peering global de rede virtual: Conectando redes virtuais em regiões de Azure.

As vantagens da utilização do peering de redes virtuais, sejam locais ou globais, incluem:

* Uma ligação de baixa latência e largura de banda alta entre os recursos em redes virtuais diferentes.
* A capacidade de recursos numa rede virtual comunicar com recursos numa rede virtual diferente.
* A capacidade de transferir dados entre redes virtuais através de subscrições do Azure, inquilinos do Azure Ative Directory, modelos de implantação e regiões de Azure.
* A capacidade de peer redes virtuais criadas através do Gestor de Recursos Azure.
* A capacidade de peer a virtual network criada através do Resource Manager para uma criada através do modelo de implementação clássico. Para saber mais sobre os modelos de implementação do Azure, veja [Understand Azure deployment models](../azure-resource-manager/management/deployment-models.md?toc=%2fazure%2fvirtual-network%2ftoc.json) (Compreender os modelos de implementação do Azure).
* Sem períodos de indisponibilidade para recursos em qualquer rede virtual durante ou após a criação do peering.

O tráfego de rede entre redes virtuais em modo de peering é privado. O tráfego entre as redes virtuais é mantido na rede principal da Microsoft. Não é necessária Internet pública, gateways ou encriptação na comunicação entre as redes virtuais.

## <a name="connectivity"></a>Conectividade

Para redes virtuais com pares, os recursos em qualquer uma das redes virtuais podem ligar-se diretamente aos recursos da rede virtual peered.

A latência de rede entre máquinas virtuais em redes virtuais no modo de peering na mesma região é igual à latência numa rede virtual individual. O débito de rede baseia-se na largura de banda que é permitida para a máquina virtual proporcionalmente ao respetivo tamanho. Não existe qualquer restrição adicional na largura de banda no peering.

O tráfego entre as máquinas virtuais nas redes virtuais em modo de peering é encaminhado diretamente através da infraestrutura principal da Microsoft e não através de um gateway ou numa Internet pública.

Pode aplicar grupos de segurança de rede em qualquer rede virtual para bloquear o acesso a outras redes virtuais ou subredes.
Ao configurar o peering de rede virtual, abra ou feche as regras do grupo de segurança da rede entre as redes virtuais. Se abrir a conectividade total entre redes virtuais epeered, pode aplicar grupos de segurança de rede para bloquear ou negar acesso específico. A conectividade completa é a opção padrão. Para saber mais sobre grupos de segurança de rede, consulte [grupos de segurança](security-overview.md).

## <a name="service-chaining"></a>Encadeamento de serviços

A corrente de serviço permite-lhe direcionar o tráfego de uma rede virtual para um aparelho virtual ou gateway numa rede esparcada através de rotas definidas pelo utilizador.

Para permitir a corrente de serviço, configure as rotas definidas pelo utilizador que apontam para máquinas virtuais em redes virtuais com pardo como o próximo endereço IP *de lúpulo.* As rotas definidas pelo utilizador também podem apontar para gateways de rede virtuais para permitir o acorrentamento do serviço.

Pode implantar redes hub-and-spoke, onde a rede virtual do hub acolhe componentes de infraestrutura, como um aparelho virtual de rede ou gateway VPN. Todas as redes virtuais “spoke” podem, então, configurar o peering com a rede virtual do concentrador. O tráfego flui através de aparelhos virtuais de rede ou gateways VPN na rede virtual do hub.

O peering de rede virtual permite que o próximo salto numa rota definida pelo utilizador seja o endereço IP de uma máquina virtual na rede virtual em modo de peering ou um gateway VPN. Não é possível encaminhar entre redes virtuais com uma rota definida pelo utilizador que especifica um gateway Azure ExpressRoute como o próximo tipo de lúpulo. Para saber mais sobre as rotas definidas pelo utilizador, veja [Descrição geral das rotas definidas pelo utilizador](virtual-networks-udr-overview.md#user-defined). Para aprender a criar um hub e topologia de rede falada, consulte [a topologia da rede hub-spoke em Azure](/azure/architecture/reference-architectures/hybrid-networking/hub-spoke?toc=%2fazure%2fvirtual-network%2ftoc.json).

## <a name="gateways-and-on-premises-connectivity"></a>Gateways e conetividade no local

Cada rede virtual, incluindo uma rede virtual esparsada, pode ter o seu próprio portal. Uma rede virtual pode usar a sua porta de entrada para se ligar a uma rede no local. Também pode configurar [ligações de rede virtual para virtual](../vpn-gateway/vpn-gateway-vnet-vnet-rm-ps.md?toc=%2fazure%2fvirtual-network%2ftoc.json) utilizando gateways, mesmo para redes virtuais com pares.

Quando configura ambas as opções para a interconectividade da rede virtual, o tráfego entre as redes virtuais flui através da configuração de peering. O tráfego usa a espinha dorsal Azure.

Também pode configurar o portal na rede virtual peered como um ponto de trânsito para uma rede no local. Neste caso, a rede virtual que está a usar um portal remoto não pode ter o seu próprio portal. Uma rede virtual tem apenas um portal. O gateway é ou um portal local ou remoto na rede virtual peered, como mostrado no seguinte diagrama:

![trânsito de peering de rede virtual](./media/virtual-networks-peering-overview/local-or-remote-gateway-in-peered-virual-network.png)

Tanto o peering da rede virtual como a rede virtual global que peering support gateway transit.

O trânsito de gateway entre redes virtuais criadas através de diferentes modelos de implementação é suportado. O portal deve estar na rede virtual no modelo De Gestão de Recursos. Para saber mais sobre como utilizar um gateway para trânsito, veja [Configurar um gateway de VPN para trânsito num peering de rede virtual](../vpn-gateway/vpn-gateway-peering-gateway-transit.md?toc=%2fazure%2fvirtual-network%2ftoc.json).

Quando você observa redes virtuais que partilham uma única ligação Azure ExpressRoute, o tráfego entre eles passa pela relação de pares. O tráfego usa a rede de espinha dorsal Azure. Pode continuar a utilizar gateways locais em cada rede virtual para ligar ao circuito no local. Caso contrário, pode utilizar um gateway partilhado e configurar o trânsito para a conectividade no local.

## <a name="troubleshoot"></a>Resolução de problemas

Para confirmar que as redes virtuais são espartadas, pode verificar rotas eficazes. Verifique as rotas para obter uma interface de rede em qualquer subrede numa rede virtual. Se um peering de rede virtual existe, todas as sub-redes na rede virtual têm rotas com o tipo de salto seguinte *VNet peering*, para cada espaço de endereços em cada virtual rede peered. Para obter mais informações, consulte [Diagnosticar um problema de encaminhamento de máquinavirtual](diagnose-network-routing-problem.md).

Também pode resolver a conectividade com uma máquina virtual numa rede virtual com o Observador da Rede Azure. Uma verificação de conectividade permite-lhe ver como o tráfego é encaminhado da interface de rede de uma máquina virtual de origem para a interface de rede de uma máquina virtual de destino. Para mais informações, consulte [as ligações de Troubleshoot com o Azure Network Watcher utilizando o portal Azure](../network-watcher/network-watcher-connectivity-portal.md#check-connectivity-to-a-virtual-machine).

Também pode experimentar os [problemas de observação da rede virtual Troubleshoot.](virtual-network-troubleshoot-peering-issues.md)

## Constrangimentos para redes virtuais espreitadas<a name="requirements-and-constraints"></a>

Os seguintes constrangimentos só se aplicam quando as redes virtuais são globalmente peered:

* Os recursos de uma rede virtual não podem comunicar com o endereço IP frontal de um Equilíbrio de Carga Interna Básica (ILB) numa rede virtual globalmente peered.
* Alguns serviços que usam um equilibrador de carga básico não funcionam sobre o peering global da rede virtual. Para mais informações, consulte [Quais são os constrangimentos relacionados com o Global VNet Peering e load Balancers?](virtual-networks-faq.md#what-are-the-constraints-related-to-global-vnet-peering-and-load-balancers)

Para mais informações, consulte [Requisitos e constrangimentos.](virtual-network-manage-peering.md#requirements-and-constraints) Para saber mais sobre o número suportado de pares, consulte [os limites](../azure-resource-manager/management/azure-subscription-service-limits.md?toc=%2fazure%2fvirtual-network%2ftoc.json#azure-resource-manager-virtual-networking-limits)de Networking .

## <a name="permissions"></a>Permissões

Para saber sobre as permissões necessárias para criar um epeering de rede virtual, consulte [Permissões](virtual-network-manage-peering.md#permissions).

## <a name="pricing"></a>Preços

Há uma taxa nominal para o tráfego de ingresse e de sugres que usa uma ligação virtual de peering de rede. Para mais informações, consulte [os preços da Rede Virtual](https://azure.microsoft.com/pricing/details/virtual-network).

Gateway Transit é uma propriedade de pares que permite a uma rede virtual utilizar um gateway VPN/ExpressRoute numa rede virtual peered. O trânsito gateway funciona tanto para as instalações cruzadas como para a conectividade rede-rede. O tráfego para o gateway (ingressou ou saída) na rede virtual peered incorre em cargas de epeering de rede virtual na VNet falada (ou VNet não gateway). Para mais informações, consulte [os preços vpN Gateway](https://azure.microsoft.com/pricing/details/vpn-gateway/) para taxas de gateway VPN e preços expressRoute Gateway para taxas de gateway ExpressRoute.

>[!NOTE]
> Uma versão anterior deste documento afirmava que as taxas de peering de rede virtuais não se aplicariam no VNet (ou VNet não gateway) com gateway Transit. Reflete agora preços precisos por página de preços.

## <a name="next-steps"></a>Passos seguintes

* Pode criar um olhar entre duas redes virtuais. As redes podem pertencer à mesma subscrição, diferentes modelos de implementação na mesma subscrição ou subscrições diferentes. Conclua um tutorial para um dos cenários seguintes:

    |Modelo de implementação do Azure             | Subscrição  |
    |---------                          |---------|
    |Ambas com Resource Manager              |[Mesma](tutorial-connect-virtual-networks-portal.md)|
    |                                   |[Diferente](create-peering-different-subscriptions.md)|
    |Uma com Resource Manager, outra com clássica  |[Mesma](create-peering-different-deployment-models.md)|
    |                                   |[Diferente](create-peering-different-deployment-models-subscriptions.md)|

* Para aprender a criar um hub e topologia de rede falada, consulte [a topologia da rede hub-spoke em Azure](/azure/architecture/reference-architectures/hybrid-networking/hub-spoke?toc=%2fazure%2fvirtual-network%2ftoc.json).
* Para saber sobre todas as definições de peering de rede virtual, consulte [Criar, alterar ou eliminar um peering](virtual-network-manage-peering.md)de rede virtual .
* Para obter respostas ao peering da rede virtual comum e às perguntas de peering da rede virtual global, consulte [vNet Peering](virtual-networks-faq.md#vnet-peering).
