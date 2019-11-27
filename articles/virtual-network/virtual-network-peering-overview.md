---
title: Emparelhamento de rede virtual do Azure
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
ms.openlocfilehash: 59854d7d46f533510bea97a6845554fc0ce83dbb
ms.sourcegitcommit: b77e97709663c0c9f84d95c1f0578fcfcb3b2a6c
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 11/22/2019
ms.locfileid: "74328351"
---
# <a name="virtual-network-peering"></a>Peering de rede virtual

O emparelhamento de rede virtual permite que você conecte diretamente as redes na [rede virtual do Azure](virtual-networks-overview.md). As redes virtuais aparecem como uma para fins de conectividade. O tráfego entre as máquinas virtuais usa a infraestrutura de backbone da Microsoft. Assim como o tráfego entre as máquinas virtuais na mesma rede, o tráfego é roteado somente pela rede *privada* da Microsoft.

O Azure dá suporte aos seguintes tipos de emparelhamento:

* Emparelhamento de rede virtual: Conecte redes virtuais na mesma região do Azure.
* Emparelhamento de rede virtual global: conectando redes virtuais em regiões do Azure.

As vantagens da utilização do peering de redes virtuais, sejam locais ou globais, incluem:

* Uma ligação de baixa latência e largura de banda alta entre os recursos em redes virtuais diferentes.
* A capacidade de recursos em uma rede virtual se comunicarem com recursos em uma rede virtual diferente.
* A capacidade de transferir dados entre redes virtuais entre as assinaturas do Azure, Azure Active Directory locatários, modelos de implantação e regiões do Azure.
* A capacidade de emparelhar redes virtuais criadas por meio do Azure Resource Manager.
* A capacidade de emparelhar uma rede virtual criada por meio do Resource Manager para uma criada por meio do modelo de implantação clássico. Para saber mais sobre os modelos de implementação do Azure, veja [Understand Azure deployment models](../azure-resource-manager/resource-manager-deployment-model.md?toc=%2fazure%2fvirtual-network%2ftoc.json) (Compreender os modelos de implementação do Azure).
* Sem períodos de indisponibilidade para recursos em qualquer rede virtual durante ou após a criação do peering.

O tráfego de rede entre redes virtuais em modo de peering é privado. O tráfego entre as redes virtuais é mantido na rede principal da Microsoft. Não é necessária Internet pública, gateways ou encriptação na comunicação entre as redes virtuais.

## <a name="connectivity"></a>Conectividade

Para redes virtuais emparelhadas, os recursos em qualquer rede virtual podem se conectar diretamente com recursos na rede virtual emparelhada.

A latência de rede entre máquinas virtuais em redes virtuais no modo de peering na mesma região é igual à latência numa rede virtual individual. O débito de rede baseia-se na largura de banda que é permitida para a máquina virtual proporcionalmente ao respetivo tamanho. Não existe qualquer restrição adicional na largura de banda no peering.

O tráfego entre as máquinas virtuais nas redes virtuais em modo de peering é encaminhado diretamente através da infraestrutura principal da Microsoft e não através de um gateway ou numa Internet pública.

Você pode aplicar grupos de segurança de rede em qualquer rede virtual para bloquear o acesso a outras redes virtuais ou sub-redes.
Ao configurar o emparelhamento de rede virtual, abra ou feche as regras do grupo de segurança de rede entre as redes virtuais. Se você abrir a conectividade completa entre redes virtuais emparelhadas, poderá aplicar grupos de segurança de rede para bloquear ou negar acesso específico. A conectividade completa é a opção padrão. Para saber mais sobre grupos de segurança de rede, consulte [grupos de segurança](security-overview.md).

## <a name="service-chaining"></a>Encadeamento de serviços

O encadeamento de serviços permite direcionar o tráfego de uma rede virtual para um dispositivo virtual ou gateway em uma rede emparelhada por meio de rotas definidas pelo usuário.

Para habilitar o encadeamento de serviços, configure rotas definidas pelo usuário que apontem para máquinas virtuais em redes virtuais emparelhadas como o endereço IP do *próximo salto* . As rotas definidas pelo usuário também podem apontar para gateways de rede virtual para habilitar o encadeamento de serviços.

Você pode implantar redes Hub e spoke, onde a rede virtual do Hub hospeda componentes de infraestrutura, como uma solução de virtualização de rede ou um gateway de VPN. Todas as redes virtuais “spoke” podem, então, configurar o peering com a rede virtual do concentrador. O tráfego flui através de dispositivos de virtualização de rede ou gateways de VPN na rede virtual do Hub.

O peering de rede virtual permite que o próximo salto numa rota definida pelo utilizador seja o endereço IP de uma máquina virtual na rede virtual em modo de peering ou um gateway VPN. Não é possível rotear entre redes virtuais com uma rota definida pelo usuário que especifique um gateway de ExpressRoute do Azure como o tipo do próximo salto. Para saber mais sobre as rotas definidas pelo utilizador, veja [Descrição geral das rotas definidas pelo utilizador](virtual-networks-udr-overview.md#user-defined). Para saber como criar uma topologia de rede hub e spoke, confira [topologia de rede hub-spoke no Azure](/azure/architecture/reference-architectures/hybrid-networking/hub-spoke?toc=%2fazure%2fvirtual-network%2ftoc.json).

## <a name="gateways-and-on-premises-connectivity"></a>Gateways e conetividade no local

Cada rede virtual, incluindo uma rede virtual emparelhada, pode ter seu próprio gateway. Uma rede virtual pode usar seu gateway para se conectar a uma rede local. Você também pode configurar [conexões de rede virtual para virtual](../vpn-gateway/vpn-gateway-vnet-vnet-rm-ps.md?toc=%2fazure%2fvirtual-network%2ftoc.json) usando gateways, mesmo para redes virtuais emparelhadas.

Quando você configura as duas opções para a interconectividade de rede virtual, o tráfego entre as redes virtuais flui por meio da configuração de emparelhamento. O tráfego usa o backbone do Azure.

Você também pode configurar o gateway na rede virtual emparelhada como um ponto de trânsito para uma rede local. Nesse caso, a rede virtual que está usando um gateway remoto não pode ter seu próprio gateway. Uma rede virtual tem apenas um gateway. O gateway é um gateway local ou remoto na rede virtual emparelhada, conforme mostrado no diagrama a seguir:

![trânsito de peering de rede virtual](./media/virtual-networks-peering-overview/local-or-remote-gateway-in-peered-virual-network.png)

O emparelhamento de rede virtual e o emparelhamento de rede virtual global dão suporte ao gateway de trânsito.

Há suporte para o tráfego de gateway entre redes virtuais criadas por meio de diferentes modelos de implantação. O gateway deve estar na rede virtual no modelo do Resource Manager. Para saber mais sobre como utilizar um gateway para trânsito, veja [Configurar um gateway de VPN para trânsito num peering de rede virtual](../vpn-gateway/vpn-gateway-peering-gateway-transit.md?toc=%2fazure%2fvirtual-network%2ftoc.json).

Quando você emparelha redes virtuais que compartilham uma única conexão do Azure ExpressRoute, o tráfego entre elas passa pela relação de emparelhamento. Esse tráfego usa a rede de backbone do Azure. Pode continuar a utilizar gateways locais em cada rede virtual para ligar ao circuito no local. Caso contrário, você pode usar um gateway compartilhado e configurar o trânsito para conectividade local.

## <a name="troubleshoot"></a>Resolução de problemas

Para confirmar se as redes virtuais estão emparelhadas, você pode verificar as rotas efetivas. Verifique as rotas para uma interface de rede em qualquer sub-rede em uma rede virtual. Se um peering de rede virtual existe, todas as sub-redes na rede virtual têm rotas com o tipo de salto seguinte *VNet peering*, para cada espaço de endereços em cada virtual rede peered. Para obter mais informações, consulte [diagnosticar um problema de roteamento de máquina virtual](diagnose-network-routing-problem.md).

Você também pode solucionar problemas de conectividade com uma máquina virtual em uma rede virtual emparelhada usando o observador de rede do Azure. Uma verificação de conectividade permite que você veja como o tráfego é roteado do adaptador de rede da máquina virtual de origem para a interface de rede da máquina virtual de destino. Para obter mais informações, consulte [solucionar problemas de conexões com o observador de rede do Azure usando o portal do Azure](../network-watcher/network-watcher-connectivity-portal.md#check-connectivity-to-a-virtual-machine).

Você também pode tentar [solucionar problemas de emparelhamento de rede virtual](virtual-network-troubleshoot-peering-issues.md).

## Restrições para redes virtuais emparelhadas<a name="requirements-and-constraints"></a>

As seguintes restrições se aplicam somente quando as redes virtuais são emparelhadas globalmente:

* Os recursos em uma rede virtual não podem se comunicar com o endereço IP de front-end de um Load Balancer interno básico (ILB) em uma rede virtual emparelhada globalmente.
* Alguns serviços que usam um balanceador de carga básico não funcionam em emparelhamento de rede virtual global. Para obter mais informações, consulte [quais são as restrições relacionadas ao emparelhamento de VNet global e aos balanceadores de carga?](virtual-networks-faq.md#what-are-the-constraints-related-to-global-vnet-peering-and-load-balancers).

Para obter mais informações, consulte [requisitos e restrições](virtual-network-manage-peering.md#requirements-and-constraints). Para saber mais sobre o número de emparelhamentos com suporte, consulte [limites de rede](../azure-subscription-service-limits.md?toc=%2fazure%2fvirtual-network%2ftoc.json#azure-resource-manager-virtual-networking-limits).

## <a name="permissions"></a>Permissões

Para saber mais sobre as permissões necessárias para criar um emparelhamento de rede virtual, consulte [permissões](virtual-network-manage-peering.md#permissions).

## <a name="pricing"></a>Preços

Há um encargo nominal para o tráfego de entrada e saída que usa uma conexão de emparelhamento de rede virtual. Para obter mais informações, consulte [preços de rede virtual](https://azure.microsoft.com/pricing/details/virtual-network).

O tráfego de gateway é uma propriedade de emparelhamento que permite que uma rede virtual utilize um gateway VPN/ExpressRoute em uma rede virtual emparelhada. O trânsito do gateway funciona para a conectividade entre locais e de rede para rede. O tráfego para o gateway (entrada ou saída) na rede virtual emparelhada incorre em encargos de emparelhamento de rede virtual. Para obter mais informações, consulte [preços de gateway de VPN](https://azure.microsoft.com/pricing/details/vpn-gateway/) para encargos de gateway de VPN e preço de gateway de expressroute para encargos de gateway de

>[!NOTE]
> Uma versão anterior deste documento declarava que os encargos de emparelhamento de rede virtual não se aplicam ao tráfego de gateway. Agora, ele reflete os preços precisos de acordo com a página de preços.

## <a name="next-steps"></a>Passos seguintes

* Você pode criar um emparelhamento entre duas redes virtuais. As redes podem pertencer à mesma assinatura, a modelos de implantação diferentes na mesma assinatura ou a assinaturas diferentes. Conclua um tutorial para um dos cenários seguintes:

    |Modelo de implementação do Azure             | Subscrição  |
    |---------                          |---------|
    |Ambas com Resource Manager              |[Mesma](tutorial-connect-virtual-networks-portal.md)|
    |                                   |[Diferente](create-peering-different-subscriptions.md)|
    |Uma com Resource Manager, outra com clássica  |[Mesma](create-peering-different-deployment-models.md)|
    |                                   |[Diferente](create-peering-different-deployment-models-subscriptions.md)|

* Para saber como criar uma topologia de rede hub e spoke, confira [topologia de rede hub-spoke no Azure](/azure/architecture/reference-architectures/hybrid-networking/hub-spoke?toc=%2fazure%2fvirtual-network%2ftoc.json).
* Para saber mais sobre todas as configurações de emparelhamento de rede virtual, confira [criar, alterar ou excluir um emparelhamento de rede virtual](virtual-network-manage-peering.md).
* Para obter respostas para o emparelhamento de rede virtual comum e as perguntas de emparelhamento de rede virtual global, consulte [emparelhamento VNet](virtual-networks-faq.md#vnet-peering).
