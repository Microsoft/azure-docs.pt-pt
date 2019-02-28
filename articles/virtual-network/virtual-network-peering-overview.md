---
title: Peering de rede Virtual do Azure
titlesuffix: Azure Virtual Network
description: Saiba mais sobre o peering de rede virtual no Azure.
services: virtual-network
documentationcenter: na
author: jimdial
ms.service: virtual-network
ms.devlang: na
ms.topic: get-started-article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 02/21/2019
ms.author: jdial
ms.openlocfilehash: 3f308c38e9fa23c36f964b117f620a39e56c9bbd
ms.sourcegitcommit: fdd6a2927976f99137bb0fcd571975ff42b2cac0
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 02/27/2019
ms.locfileid: "56958189"
---
# <a name="virtual-network-peering"></a>Peering de rede virtual

O peering de rede virtual permite ao utilizador ligar duas [redes virtuais](virtual-networks-overview.md) do Azure de forma totalmente integrada. Uma vez executado o peering, as redes virtuais aparecem como uma única, para fins de conectividade. O tráfego entre máquinas virtuais nas redes virtuais em modo de peering será encaminhado através da infraestrutura principal da Microsoft, tal como o tráfego é encaminhado entre máquinas virtuais na mesma rede virtual através apenas de endereços IP *privados*. O Azure suporta:
* VNet Peering - Ligar VNets na mesma região do Azure
* Global VNET Peering - Ligar VNets entre regiões do Azure

As vantagens da utilização do peering de redes virtuais, sejam locais ou globais, incluem:

* O tráfego de rede entre redes virtuais em modo de peering é privado. O tráfego entre as redes virtuais é mantido na rede principal da Microsoft. Não é necessária Internet pública, gateways ou encriptação na comunicação entre as redes virtuais.
* Uma ligação de baixa latência e largura de banda alta entre os recursos em redes virtuais diferentes.
* A capacidade dos recursos numa rede virtual comunicarem com os recursos numa rede virtual diferente, assim que as redes virtuais forem colocadas em modo de peering.
* A capacidade de transferir dados entre subscrições do Azure, modelos de implementação e entre regiões do Azure.
* A capacidade de configurar o peering entre redes virtuais criadas através do Azure Resource Manager ou o peering entre uma rede virtual criada através do Resource Manager e outra criada através do modelo de implementação clássica. Para saber mais sobre os modelos de implementação do Azure, veja [Understand Azure deployment models](../azure-resource-manager/resource-manager-deployment-model.md?toc=%2fazure%2fvirtual-network%2ftoc.json) (Compreender os modelos de implementação do Azure).
* Sem períodos de indisponibilidade para recursos em qualquer rede virtual durante ou após a criação do peering.

## <a name="connectivity"></a>Conectividade

Depois das redes virtuais serem colocadas em modo de peering, os recursos em cada uma destas podem ligar-se diretamente a recursos na rede virtual em modo de peering.

A latência de rede entre máquinas virtuais em redes virtuais no modo de peering na mesma região é igual à latência numa rede virtual individual. O débito de rede baseia-se na largura de banda que é permitida para a máquina virtual proporcionalmente ao respetivo tamanho. Não existe qualquer restrição adicional na largura de banda no peering.

O tráfego entre as máquinas virtuais nas redes virtuais em modo de peering é encaminhado diretamente através da infraestrutura principal da Microsoft e não através de um gateway ou numa Internet pública.

Os grupos de segurança de rede podem ser aplicados a qualquer rede virtual para bloquear o acesso a outras redes virtuais ou sub-redes, se assim o desejar.
Ao configurar o peering de rede virtual, pode abrir ou fechar as regras do grupo de segurança de rede entre as redes virtuais. Se abrir a conectividade total entre as redes virtuais em modo de peering (que é a opção predefinida), pode aplicar grupos de segurança de rede a sub-redes ou máquinas virtuais específicas, para bloquear ou negar o acesso específico. Para saber mais sobre os grupos de segurança de rede, veja [Descrição geral dos grupos de segurança de rede](security-overview.md).

## <a name="service-chaining"></a>Encadeamento de serviços

Para ativar o encadeamento de serviços, pode configurar rotas definidas pelo utilizador que apontem para máquinas virtuais em redes virtuais em modo de peering como o endereço IP de *próximo salto* ou para gateways de rede virtuais. O encadeamento se serviços permite-lhe encaminhar o tráfego de uma rede virtual para um dispositivo de rede numa rede virtual, ou gateways de rede virtual, em modo de peering através de rotas definidas pelo utilizador.

Pode implementar redes "hub-and-spoke", nas quais o hub de rede virtual pode alojar componentes de infraestruturas tais como aplicações virtuais de rede ou gateways VPN. Todas as redes virtuais “spoke” podem, então, configurar o peering com a rede virtual do concentrador. O tráfego pode fluir dos dispositivos de rede virtual ou gateways VPN na rede virtual do concentrador. 

O peering de rede virtual permite que o próximo salto numa rota definida pelo utilizador seja o endereço IP de uma máquina virtual na rede virtual em modo de peering ou um gateway VPN. No entanto, não é possível encaminhar rotas entre redes virtuais com uma rota definida pelo utiliza a especificar um gateway ExpressRoute como o tipo de salto seguinte. Para saber mais sobre as rotas definidas pelo utilizador, veja [Descrição geral das rotas definidas pelo utilizador](virtual-networks-udr-overview.md#user-defined). Para saber como criar uma topologia de rede hub-and-spoke, veja [Topologia de rede hub-and-spoke](/azure/architecture/reference-architectures/hybrid-networking/hub-spoke?toc=%2fazure%2fvirtual-network%2ftoc.json).

## <a name="gateways-and-on-premises-connectivity"></a>Gateways e conetividade no local

Cada rede virtual, independentemente de estar ou não em modo de peering com outra, pode continuar a ter um gateway próprio e a utilizá-lo para se ligar a uma rede no local. Também pode configurar [ligações de rede virtual a rede virtual](../vpn-gateway/vpn-gateway-vnet-vnet-rm-ps.md?toc=%2fazure%2fvirtual-network%2ftoc.json) através de gateways, mesmo que as redes virtuais estejam em modo de peering.

Quando ambas as opções de inter-conetividade de rede virtual estiverem configuradas, o tráfego entre as redes virtuais irá fluir através da configuração em modo de peering (ou seja, através da estrutura principal do Azure).

Quando as redes virtuais estão em modo de peering na mesma região, também pode configurar o gateway na rede virtual em modo de peering como um ponto de trânsito para uma rede no local. Neste caso, a rede virtual que está a utilizar um gateway remoto não pode ter um gateway próprio. Uma rede virtual pode ter apenas um gateway. O gateway pode ser local ou remoto (na rede virtual em modo de peering), conforme mostrado na imagem seguinte:

![trânsito de peering de rede virtual](./media/virtual-networks-peering-overview/figure04.png)

O trânsito de gateway não é suportado na relação de peering entre redes virtuais criadas em regiões diferentes. Para que o trânsito de gateway funcione, ambas as redes virtuais na relação de peering têm de existir na mesma região. O trânsito de gateway entre redes virtuais criadas com modelos de implementação diferentes (Resource Manager e clássica), só é suportada se o gateway (VPN ou ExpressRoute) está na rede virtual (Resource Manager). Para saber mais sobre como utilizar um gateway para trânsito, veja [Configurar um gateway de VPN para trânsito num peering de rede virtual](../vpn-gateway/vpn-gateway-peering-gateway-transit.md?toc=%2fazure%2fvirtual-network%2ftoc.json).

Quando as redes virtuais que partilham uma única ligação ExpressRoute do Azure estão em modo de peering, o tráfego entre as mesmas passa pela relação de peering (ou seja, pela rede principal do Azure). Pode continuar a utilizar gateways locais em cada rede virtual para ligar ao circuito no local. Em alternativa, pode utilizar um gateway partilhado e configurar o trânsito para conectividade no local.

## <a name="troubleshoot"></a>Resolução de problemas

Para confirmar o peering de uma rede virtual peering, pode [verificar as rotas efetivas](diagnose-network-routing-problem.md) para uma interface de rede em qualquer sub-rede numa rede virtual. Se um peering de rede virtual existe, todas as sub-redes na rede virtual têm rotas com o tipo de salto seguinte *VNet peering*, para cada espaço de endereços em cada virtual rede peered.

Também pode resolver problemas relacionados com a conectividade a uma máquina virtual numa rede virtual em modo de peering através da [verificação de conectividade](../network-watcher/network-watcher-connectivity-portal.md?toc=%2fazure%2fvirtual-network%2ftoc.json) do Observador de Rede. A verificação da conectividade permite-lhe ver como o tráfego é encaminhado da interface de rede de uma máquina de virtual de origem para a interface de rede de uma máquina de virtual de destino.

Também pode tentar o [resolução de problemas de problemas de peering de rede virtual](https://support.microsoft.com/help/4486956/troubleshooter-for-virtual-network-peering-issues).

## <a name="requirements-and-constraints"></a>Requisitos e limitações

As seguintes restrições aplicam-se apenas quando as redes virtuais global em modo de peering:
- Os recursos numa rede virtual não conseguem comunicar com o endereço IP do front-end de um balanceador de carga interno do Azure na rede virtual em modo de peering global. O balanceador de carga e os recursos que comunicam com o mesmo têm de estar na mesma região.
- Não pode utilizar gateways remotos ou permitir trânsito de gateway. Para utilizar gateways remotos ou permitir trânsito de gateway, as redes virtuais em modo de peering têm de estar na mesma região.

Para saber mais sobre os requisitos e as limitações, veja [Requisitos e limitações do peering de rede virtual](virtual-network-manage-peering.md#requirements-and-constraints). Para saber mais sobre os limites ao número de peerings que pode criar para uma rede virtual, veja [Azure networking limits](../azure-subscription-service-limits.md?toc=%2fazure%2fvirtual-network%2ftoc.json#azure-resource-manager-virtual-networking-limits) (Limites de rede do Azure). 

## <a name="permissions"></a>Permissões

Para saber mais sobre as permissões necessárias para criar um peering de rede virtual, veja [Virtual network peering permissions](virtual-network-manage-peering.md#permissions) (Permissões de peering de rede virtual).

## <a name="pricing"></a>Preços

Existe uma cobrança nominal para o tráfego de entrada e de saída que utilize uma ligação de peering de rede virtual. Para obter mais informações sobre o VNet Peering e os preços do Global VNet Peering, veja a [página de preços](https://azure.microsoft.com/pricing/details/virtual-network).

O trânsito do gateway é uma propriedade de peering que permite a uma rede virtual utilizar um gateway de VPN na rede virtual no modo de peering e, assim, permitir a conetividade entre vários locais ou VNet a VNet. O tráfego que passa através de um gateway remoto neste cenário está sujeito a [custos de gateway de VPN](https://azure.microsoft.com/pricing/details/vpn-gateway/) e não incorre em [custos de VNet peering](https://azure.microsoft.com/pricing/details/virtual-network). Por exemplo, se a VNetA tiver um gateway de VPN para conectividade no local e a VNetB estiver ligada à VNetA com as propriedades adequadas configuradas, o tráfego da VNetB no local apenas é cobrado por gateway VPN. Não se aplicam custos de VNet peering. Saiba como [configurar o trânsito do gateway de VPN para peering de rede virtual](../vpn-gateway/vpn-gateway-peering-gateway-transit.md?toc=%2fazure%2fvirtual-network%2ftoc.json)

## <a name="next-steps"></a>Passos Seguintes

* É criado um peering de rede virtual entre redes virtuais criadas com o mesmo ou com diferentes modelos de implementação que existam na mesma ou em diferentes subscrições. Conclua um tutorial para um dos cenários seguintes:

    |Modelo de implementação do Azure             | Subscrição  |
    |---------                          |---------|
    |Ambas com Resource Manager              |[Mesma](tutorial-connect-virtual-networks-portal.md)|
    |                                   |[Diferente](create-peering-different-subscriptions.md)|
    |Uma com Resource Manager, outra com clássica  |[Mesma](create-peering-different-deployment-models.md)|
    |                                   |[Diferente](create-peering-different-deployment-models-subscriptions.md)|

* Saiba como criar uma [topologia de rede hub-and-spoke](/azure/architecture/reference-architectures/hybrid-networking/hub-spoke?toc=%2fazure%2fvirtual-network%2ftoc.json).
* Saiba tudo sobre [as definições de peering de rede virtual e como alterá-las](virtual-network-manage-peering.md).
* Obtenha respostas a perguntas comuns de Peering de VNet e Peering de VNet Global através do nosso [FAQ sobre Peering de VNet](virtual-networks-faq.md#vnet-peering)
