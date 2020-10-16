---
title: Conectividade de rede cruzada Azure
description: Esta página descreve um cenário de aplicação para conectividade de rede cruzada e solução com base em funcionalidades de networking Azure.
services: expressroute
author: duongau
ms.service: expressroute
ms.topic: article
ms.date: 04/03/2019
ms.author: duau
ms.openlocfilehash: f13e3c03c0cfd747c7819d95a5fb98560db861c4
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/09/2020
ms.locfileid: "89398074"
---
# <a name="cross-network-connectivity"></a>Conectividade entre redes

A Fabrikam Inc. tem uma grande presença física e implantação da Azure no Leste dos EUA. A Fabrikam tem conectividade de back-end entre as suas implementações no local e a azure via ExpressRoute. Da mesma forma, a Contoso Ltd. tem uma presença e implantação da Azure no Oeste dos EUA. A Contoso tem conectividade de back-end entre as suas implementações no local e a Azure via ExpressRoute.  

A Fabrikam Inc. adquire a Contoso Ltd. Após a fusão, Fabrikam quer interligar as redes. A seguinte figura ilustra o cenário:

![O cenário de Aplicação](./media/cross-network-connectivity/premergerscenario.png)

As setas tracejadas no meio da figura acima indicam as interligações de rede desejadas. Especificamente, existem três tipos de ligações cruzadas desejadas: 1) Fabrikam e Contoso VNets cruz ligam, 2) Cross regional on-in e VNets cross connects (isto é, ligar a rede Fabrikam no local à Contoso VNet e ligar a rede de Contoso no local à Fabrikam VNet) e 3) Fabrikam e Contoso no local da rede cross connect. 

A tabela seguinte mostra a tabela de rotas do espreguiçadamento privado do ExpressRoute da Contoso Ltd., antes da fusão.

![Quadro de rotas Contoso ExpressRoute antes da fusão](./media/cross-network-connectivity/contosoexr-rt-premerger.png)

O quadro seguinte mostra as rotas efetivas de um VM na assinatura Contoso, antes da fusão. De acordo com a tabela, o VM no VNet tem conhecimento do espaço de endereço VNet e da rede Contoso no local, para além das predefinições.

![Rotas da Contoso VM antes da fusão](./media/cross-network-connectivity/contosovm-routes-premerger.png)

A tabela seguinte mostra a tabela de rotas do espreguicimento privado do ExpressRoute da Fabrikam Inc., antes da fusão.

![Mesa de rota Fabrikam ExpressRoute antes da fusão](./media/cross-network-connectivity/fabrikamexr-rt-premerger.png)

O quadro seguinte mostra as rotas efetivas de um VM na assinatura Fabrikam, antes da fusão. De acordo com a tabela, o VM no VNet tem conhecimento do espaço de endereço VNet e da rede Fabrikam no local, para além das padrão.

![Rotas da Fabrikam VM antes da fusão](./media/cross-network-connectivity/fabrikamvm-routes-premerger.png)

Neste artigo, vamos passar passo a passo e discutir como alcançar as ligações cruzadas desejadas usando as seguintes funcionalidades da rede Azure:

* [Peering de rede virtual][Virtual network peering] 
* [Ligação ExpressRoute de rede virtual][connection]
* [Alcance Global][Global Reach] 

## <a name="cross-connecting-vnets"></a>Cruzando VNets de ligação

O espremiamento de rede virtual (o espremiamento VNet) proporciona o melhor e melhor desempenho da rede ao ligar duas redes virtuais. O espremiamento vNet suporta o espreício de dois VNets, tanto na mesma região de Azure (vulgarmente chamada de observação VNet) como em duas regiões Azure diferentes (vulgarmente chamadas de espreitar VNet Global). 

Vamos configurar a Global VNet a espreitar entre as subscrições VNets em Contoso e Fabrikam Azure. Para criar a rede virtual que espreita entre duas redes virtuais, consulte Criar um artigo [de observação de rede virtual.][Configure VNet peering]

A imagem a seguir mostra a arquitetura da rede depois de configurar o olhar global vNet.

![A Arquitetura depois do vNet-peering](./media/cross-network-connectivity/vnet-peering.png )

A tabela seguinte mostra as rotas conhecidas da assinatura Contoso VM. Preste atenção à última entrada da mesa. Esta entrada é o resultado da ligação cruzada das redes virtuais.

![Rotas da Contoso VM após o espreitar pela VNet](./media/cross-network-connectivity/contosovm-routes-peering.png)

A tabela seguinte mostra as rotas conhecidas da VM de subscrição de Fabrikam. Preste atenção à última entrada da mesa. Esta entrada é o resultado da ligação cruzada das redes virtuais.

![Rotas de Fabrikam VM após o espremiamento da VNet](./media/cross-network-connectivity/fabrikamvm-routes-peering.png)

O vNet a espreitar diretamente duas redes virtuais (ver não há próximo salto para a entrada *VNetGlobalPeering* nas duas tabelas acima)

## <a name="cross-connecting-vnets-to-the-on-premises-networks"></a>Cruzando as Redes de Ligação às redes no local

Podemos ligar um circuito ExpressRoute a várias redes virtuais. Consulte [os limites de subscrição e de serviço][Subscription limits] para o número máximo de redes virtuais que podem ser ligadas a um circuito ExpressRoute. 

Vamos ligar o circuito Fabrikam ExpressRoute à subscrição de Contoso VNet e igualmente ao circuito Contoso ExpressRoute à subscrição de Fabrikam VNet para permitir a conectividade cruzada entre redes virtuais e as redes no local. Para ligar uma rede virtual a um circuito ExpressRoute numa subscrição diferente, precisamos de criar e usar uma autorização.  Ver o artigo: [Ligue uma rede virtual a um circuito ExpressRoute][Connect-ER-VNet].

A imagem que se segue mostra a arquitetura da rede depois de configurar a conectividade cruzada ExpressRoute para as redes virtuais.

![A Arquitetura depois da ligação cruzada ExpressRoutes](./media/cross-network-connectivity/exr-x-connect.png)

A tabela seguinte mostra a tabela de rotas do espreguiçadamento privado do ExpressRoute da Contoso Ltd., depois de cruzar as redes virtuais às redes de bordo via ExpressRoute. Veja se a tabela de rotas tem rotas pertencentes a ambas as redes virtuais.

![Quadro de rota Contoso ExpressRoute após cruzamento de ExR e VNets](./media/cross-network-connectivity/contosoexr-rt-xconnect.png)

A tabela seguinte mostra a tabela de rotas do espreguicimento privado do ExpressRoute da Fabrikam Inc., depois de cruzar as redes virtuais às redes de instalações via ExpressRoute. Veja se a tabela de rotas tem rotas pertencentes a ambas as redes virtuais.

![Tabela de rota Fabrikam ExpressRoute após cruzamento de ExR e VNets](./media/cross-network-connectivity/fabrikamexr-rt-xconnect.png)

A tabela seguinte mostra as rotas conhecidas da assinatura Contoso VM. Preste atenção às entradas de gateway de *rede virtual* da tabela. O VM vê rotas para ambas as redes no local.

![Rotas da Contoso VM após cruzamento de ExR e VNets](./media/cross-network-connectivity/contosovm-routes-xconnect.png)

A tabela seguinte mostra as rotas conhecidas da VM de subscrição de Fabrikam. Preste atenção às entradas de gateway de *rede virtual* da tabela. O VM vê rotas para ambas as redes no local.

![Rotas Fabrikam VM após cruzamento de ExR e VNets](./media/cross-network-connectivity/fabrikamvm-routes-xconnect.png)

>[!NOTE]
>Nas assinaturas Fabrikam e/ou Contoso também pode ter falado VNets para o respetivo hub VNet (um hub e design de fala não é ilustrado nos diagramas de arquitetura neste artigo). As ligações cruzadas entre as portas do hub VNet para o ExpressRoute também permitirão a comunicação entre os centros leste e oeste e os raios de porta-vozes.
>

## <a name="cross-connecting-on-premises-networks"></a>Redes de ligação cruzada no local

O ExpressRoute Global Reach proporciona conectividade entre redes no local que estão ligadas a diferentes circuitos ExpressRoute. Vamos configurar o Alcance Global entre os circuitos Contoso e Fabrikam ExpressRoute. Como os circuitos ExpressRoute estão em diferentes subscrições, precisamos criar e usar uma autorização. Consulte o artigo [Configurar ExpressRoute Global Reach][Configure Global Reach] para orientação passo a passo.

A imagem que se segue mostra a arquitetura da rede depois de configurar o Global Reach.

![A Arquitetura depois de configurar o Alcance Global](./media/cross-network-connectivity/globalreach.png)

A tabela seguinte mostra a tabela de rotas do espreguiçadamento privado do ExpressRoute da Contoso Ltd., depois de configurar o Global Reach. Veja se a tabela de rotas tem rotas pertencentes a ambas as redes no local. 

![Quadro de rotas Contoso ExpressRoute após Alcance Global](./media/cross-network-connectivity/contosoexr-rt-gr.png)

A tabela seguinte mostra a tabela de rotas do espreguite privado do ExpressRoute da Fabrikam Inc., depois de configurar o Global Reach. Veja se a tabela de rotas tem rotas pertencentes a ambas as redes no local.

![Mesa de rota Fabrikam ExpressRoute após Alcance Global]( ./media/cross-network-connectivity/fabrikamexr-rt-gr.png )

## <a name="next-steps"></a>Passos seguintes

Consulte [a rede virtual FAQ,][VNet-FAQ]para mais perguntas sobre o VNet e o VNet-peering. Consulte [o ExpressRoute FAQ][ER-FAQ] para mais perguntas sobre a conectividade ExpressRoute e rede virtual.

O Global Reach é lançado numa base país/região por país/região. Para ver se o Global Reach está disponível nos países/regiões que deseja, consulte [o ExpressRoute Global Reach][Global Reach].

<!--Link References-->
[Virtual network peering]: https://docs.microsoft.com/azure/virtual-network/virtual-network-peering-overview
[connection]: https://docs.microsoft.com/azure/expressroute/expressroute-howto-linkvnet-portal-resource-manager
[Global Reach]: https://docs.microsoft.com/azure/expressroute/expressroute-global-reach
[Configure VNet peering]: https://docs.microsoft.com/azure/virtual-network/create-peering-different-subscriptions
[Configure Global Reach]: https://docs.microsoft.com/azure/expressroute/expressroute-howto-set-global-reach
[Subscription limits]: https://docs.microsoft.com/azure/azure-resource-manager/management/azure-subscription-service-limits#networking-limits
[Connect-ER-VNet]: https://docs.microsoft.com/azure/expressroute/expressroute-howto-linkvnet-portal-resource-manager
[ER-FAQ]: https://docs.microsoft.com/azure/expressroute/expressroute-faqs
[VNet-FAQ]: https://docs.microsoft.com/azure/virtual-network/virtual-networks-faq