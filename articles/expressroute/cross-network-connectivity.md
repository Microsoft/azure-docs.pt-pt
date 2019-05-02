---
title: Conectividade de rede do Azure | Documentos da Microsoft
description: Esta página descreve um cenário de aplicação para cruzada conectividade de rede e de solução com base nas funcionalidades de rede do Azure.
documentationcenter: na
services: networking
author: rambk
manager: tracsman
ms.service: expressroute
ms.topic: article
ms.workload: infrastructure-services
ms.date: 04/03/2019
ms.author: rambala
ms.openlocfilehash: 3bc189cf269084fdb26f141a36755c96554cad7b
ms.sourcegitcommit: e7d4881105ef17e6f10e8e11043a31262cfcf3b7
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/29/2019
ms.locfileid: "64865993"
---
# <a name="cross-network-connectivity"></a>Conectividade entre redes

Fabrikam Inc. tem um grande presença física e a implementação do Azure na região E.U.A. Leste. A Fabrikam tem conectividade de back-end entre o local e implementações do Azure através do ExpressRoute. Da mesma forma, Contoso Ltd. tem uma presença e a implementação do Azure em E.U.A. oeste. A Contoso tiver conectividade de back-end entre o local e implementações do Azure através do ExpressRoute.  

Fabrikam Inc. adquire Contoso Ltd. Seguindo a intercalação de pedidos, a Fabrikam quer interligar as redes. A figura seguinte ilustra o cenário:

 [![1]][1]

As setas tracejadas no meio da figura acima indicam as interconexões de rede pretendido. Especificamente, existem três tipos de ligações cruzadas pretendidas: Ligar-se a Fabrikam e Contoso VNets cruzada de 1), 2) cruzada regional no local e VNets em vários liga (que é, a ligação de rede de locais de Fabrikam a Contoso VNet e ligar a rede no local de Contoso a Fabrikam VNet) e a Fabrikam 3) e a Contoso ligar a rede no local cruzada. 

A tabela seguinte mostra a tabela de rotas do peering privado do ExpressRoute da Contoso Ltd., antes da fusão.

[![2]][2]

A tabela seguinte mostra as rotas efetivas de uma VM na subscrição Contoso, antes da fusão. Por tabela, a VM na VNet está ciente do espaço de endereços da VNet e de rede no local da Contoso, além das implementações padrão. 

[![4]][4]

A tabela seguinte mostra a tabela de rotas do peering privado do ExpressRoute da Fabrikam Inc., antes da fusão.

[![3]][3]

A tabela seguinte mostra as rotas efetivas de uma VM na subscrição Fabrikam, antes da fusão. Por tabela, a VM na VNet está ciente do espaço de endereços da VNet e a rede no local do Fabrikam, além das implementações padrão.

[![5]][5]

Neste artigo, vamos percorrer passo a passo e discutir como atingir as ligações cruzadas pretendidas utilizando as seguintes funcionalidades de rede do Azure:

* [Peering de rede virtual][Virtual network peering] 
* [Ligação do ExpressRoute de rede virtual][connection]
* [Alcance global][Global Reach] 

## <a name="cross-connecting-vnets"></a>Cross ligar VNets

Peering de rede virtual (VNet peering) fornece mais ideal e o melhor desempenho de rede quando ligar duas redes virtuais. O VNet peering suporta peering duas VNets na mesma região do Azure (normalmente chamado de VNet peering) e em duas regiões do Azure diferentes (normalmente chamado de Global VNet peering). 

Vamos configurar o Global VNet peering entre VNets em subscrições Contoso e Fabrikam Azure. Para saber como criar a peering entre duas redes virtuais de rede virtual, consulte [criar um peering de rede virtual] [ Configure VNet peering] artigo.

A imagem seguinte mostra a arquitetura de rede depois de configurar o Global VNet peering.

[![6]][6]

A tabela seguinte mostra as rotas conhecidos para a subscrição de Contoso VM. Preste atenção à última entrada da tabela. Esta entrada é o resultado da cruzada ligar as redes virtuais.

[![7]][7]

A tabela seguinte mostra as rotas conhecidos para a subscrição da Fabrikam VM. Preste atenção à última entrada da tabela. Esta entrada é o resultado da cruzada ligar as redes virtuais.

[![8]][8]

Diretamente o VNet peering liga duas redes virtuais (consulte, não há nenhum próximo salto para *VNetGlobalPeering* entrada em duas tabelas acima)

## <a name="cross-connecting-vnets-to-the-on-premises-networks"></a>Em várias VNets a ligar às redes no local

Um circuito do ExpressRoute podermos ligar a várias redes virtuais. Ver [subscrição e limites do serviço] [ Subscription limits] para o número máximo de redes virtuais que podem ser ligadas a um circuito do ExpressRoute. 

Vamos ligar o circuito de Fabrikam ExpressRoute a subscrição de Contoso VNet e da mesma forma, o circuito do ExpressRoute de Contoso à subscrição de Fabrikam VNet para ativar a conectividade cruzada entre redes virtuais e as redes no local. Para ligar uma rede virtual a um circuito do ExpressRoute numa subscrição diferente, é necessário criar e utilizar uma autorização.  Consulte o artigo: [Ligar uma rede virtual a um circuito do ExpressRoute][Connect-ER-VNet].

A imagem seguinte mostra a arquitetura de rede depois de configurar o ExpressRoute em várias conectividade para as redes virtuais.

[![9]][9]

A tabela seguinte mostra a tabela de rotas do peering privado do ExpressRoute da Contoso Ltd., após cruzada ligar redes virtuais para as redes no local através do ExpressRoute. Ver que a tabela de rotas tem rotas que pertencem a ambas as redes virtuais.

[![10]][10]

A tabela seguinte mostra a tabela de rotas do peering privado do ExpressRoute da Fabrikam Inc., após cruzada ligar redes virtuais para as redes no local através do ExpressRoute. Ver que a tabela de rotas tem rotas que pertencem a ambas as redes virtuais.

[![11]][11]

A tabela seguinte mostra as rotas conhecidos para a subscrição de Contoso VM. Preste atenção às *gateway de rede Virtual* entradas da tabela. A VM vê as rotas para ambas as redes no local.

[![12]][12]

A tabela seguinte mostra as rotas conhecidos para a subscrição da Fabrikam VM. Preste atenção às *gateway de rede Virtual* entradas da tabela. A VM vê as rotas para ambas as redes no local.

[![13]][13]

>[!NOTE]
>Em qualquer um as subscrições da Fabrikam e/ou Contoso que também é possível ter VNets spoke para o hub respectivo VNet (um design de hub- and -spoke não é ilustrado nos diagramas de arquitetura neste artigo). As ligações cruzadas entre os gateways de VNet do hub para ExpressRoute também irão permitir a comunicação entre hubs East e West- and -spoke.
>

## <a name="cross-connecting-on-premises-networks"></a>Cruzar a ligação de redes no local

Alcance Global do ExpressRoute fornece conectividade entre redes no local que estão ligados a diferentes circuitos do ExpressRoute. Vamos configurar o alcance Global entre circuitos Contoso e Fabrikam ExpressRoute. Como os circuitos do ExpressRoute estão em subscrições diferentes, temos de criar e utilizar uma autorização. Ver [configurar o ExpressRoute alcance Global] [ Configure Global Reach] artigo para obter orientações passo a passo.

A imagem seguinte mostra a arquitetura de rede depois de configurar o alcance Global.

[![14]][14]

A tabela seguinte mostra a tabela de rotas do peering privado do ExpressRoute da Contoso Ltd., depois de configurar o alcance Global. Ver que a tabela de rotas tem rotas que pertencem a ambas as redes no local. 

[![15]][15]

A tabela seguinte mostra a tabela de rotas do peering privado do ExpressRoute da Fabrikam Inc., depois de configurar o alcance Global. Ver que a tabela de rotas tem rotas que pertencem a ambas as redes no local.

[![16]][16]

## <a name="next-steps"></a>Passos Seguintes

Ver [FAQ da rede virtual][VNet-FAQ], para quaisquer perguntas ainda mais na VNet e o peering de VNet. Ver [FAQ do ExpressRoute] [ ER-FAQ] para conectividade de rede mais alguma questão no ExpressRoute e virtuais.

Alcance global é implementada numa base de país/região por país/região. Para ver se está disponível nos países/regiões que pretende que o alcance Global, veja [alcance Global do ExpressRoute][Global Reach].

<!--Image References-->
[1]: ./media/cross-network-connectivity/premergerscenario.png "cenário da aplicação"
[2]: ./media/cross-network-connectivity/contosoexr-rt-premerger.png "tabela de rotas de Contoso ExpressRoute antes de intercalação de pedidos"
[3]: ./media/cross-network-connectivity/fabrikamexr-rt-premerger.png "tabela de rotas de Fabrikam ExpressRoute antes da intercalação de pedidos"
[4]: ./media/cross-network-connectivity/contosovm-routes-premerger.png "Contoso VM encaminha antes da intercalação de pedidos"
[5]: ./media/cross-network-connectivity/fabrikamvm-routes-premerger.png "Fabrikam VM encaminha antes da intercalação de pedidos"
[6]: ./media/cross-network-connectivity/vnet-peering.png "a arquitetura após o VNet peering"
[7]: ./media/cross-network-connectivity/contosovm-routes-peering.png "Contoso VM encaminha após o VNet peering"
[8]: ./media/cross-network-connectivity/fabrikamvm-routes-peering.png "Fabrikam VM encaminha após o VNet peering"
[9]: ./media/cross-network-connectivity/exr-x-connect.png "a arquitetura depois expressroutes ao qual cross ligação"
[10]: ./media/cross-network-connectivity/contosoexr-rt-xconnect.png "tabela de rotas do ExpressRoute de Contoso após cruzada ligação ExR e VNets"
[11]: ./media/cross-network-connectivity/fabrikamexr-rt-xconnect.png "tabela de rotas de Fabrikam ExpressRoute após cruzada ligação ExR e VNets"
[12]: ./media/cross-network-connectivity/contosovm-routes-xconnect.png "em várias rotas de Contoso VM após a ligação ExR e VNets"
[13]: ./media/cross-network-connectivity/fabrikamvm-routes-xconnect.png "em várias rotas Fabrikam VM após a ligação ExR e VNets"
[14]: ./media/cross-network-connectivity/globalreach.png "a arquitetura depois de configurar o alcance Global"
[15]: ./media/cross-network-connectivity/contosoexr-rt-gr.png "tabela de rota do ExpressRoute de Contoso após o alcance Global"
[16]: ./media/cross-network-connectivity/fabrikamexr-rt-gr.png "tabela de rotas de Fabrikam ExpressRoute após o alcance Global"

<!--Link References-->
[Virtual network peering]: https://docs.microsoft.com/azure/virtual-network/virtual-network-peering-overview
[connection]: https://docs.microsoft.com/azure/expressroute/expressroute-howto-linkvnet-portal-resource-manager
[Global Reach]: https://docs.microsoft.com/azure/expressroute/expressroute-global-reach
[Configure VNet peering]: https://docs.microsoft.com/azure/virtual-network/create-peering-different-subscriptions
[Configure Global Reach]: https://docs.microsoft.com/azure/expressroute/expressroute-howto-set-global-reach
[Subscription limits]: https://docs.microsoft.com/azure/azure-subscription-service-limits#networking-limits
[Connect-ER-VNet]: https://docs.microsoft.com/azure/expressroute/expressroute-howto-linkvnet-portal-resource-manager
[ER-FAQ]: https://docs.microsoft.com/azure/expressroute/expressroute-faqs
[VNet-FAQ]: https://docs.microsoft.com/azure/virtual-network/virtual-networks-faq