---
title: Conectividade transversal azure
description: Esta página descreve um cenário de aplicação para conectividade e solução de rede cruzada com base em funcionalidades de rede Azure.
services: expressroute
author: rambk
ms.service: expressroute
ms.topic: article
ms.date: 04/03/2019
ms.author: rambala
ms.openlocfilehash: 48ec26cc98310dfeb61aa17018c940b431cfbcee
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/27/2020
ms.locfileid: "75644261"
---
# <a name="cross-network-connectivity"></a>Conectividade entre redes

A Fabrikam Inc. tem uma grande presença física e implantação azure no Leste dos EUA. A Fabrikam tem conectividade de back-end entre as suas implementações no local e as implementações do Azure via ExpressRoute. Da mesma forma, a Contoso Ltd. tem presença e implantação azure no Oeste dos EUA. A Contoso tem conectividade back-end entre as suas implementações no local e as implantações do Azure via ExpressRoute.  

Fabrikam Inc. adquire Contoso Ltd. Após a fusão, Fabrikam quer interligar as redes. A figura que se segue ilustra o cenário:

 [![11]][1]

As setas tracejadas no meio da figura acima indicam as interligações de rede desejadas. Especificamente, existem três tipos de ligações cruzadas desejadas: 1) Fabrikam e Contoso VNets cruzam ligação, 2) Cruzamento regional no local e ligações cruzadas VNets (isto é, ligando a rede de instalações de Fabrikam ao Contoso VNet e ligando Contoso rede no local para Fabrikam VNet), e 3) Fabrikam e Contoso on-premise network cross connect. 

A tabela que se segue mostra a tabela de rotas do peering privado da Rota Expresso de Contoso Ltd., antes da fusão.

[![2]][2]

O quadro seguinte mostra as rotas efetivas de um VM na assinatura Contoso, antes da fusão. Por tabela, o VM no VNet está ciente do espaço de endereço VNet e da rede Decontoso on-instalações, além dos padrão. 

[![4]][4]

A tabela que se segue mostra a tabela de rotas do olhar privado da Rota Expresso da Fabrikam Inc., antes da fusão.

[![3 3]][3]

O quadro seguinte mostra as rotas efetivas de um VM na assinatura Fabrikam, antes da fusão. Por tabela, o VM no VNet está ciente do espaço de endereço VNet e da rede de instalações Fabrikam, além das predefinidas.

[![5]][5]

Neste artigo, vamos passo a passo e discutir como alcançar as conexões cruzadas desejadas usando as seguintes funcionalidades da rede Azure:

* [Peering de rede virtual][Virtual network peering] 
* [Conexão Rede Virtual ExpressRoute][connection]
* [Alcance Global][Global Reach] 

## <a name="cross-connecting-vnets"></a>VNets de ligação cruzada

O peering de rede virtual (peering VNet) fornece o melhor desempenho de rede ao ligar duas redes virtuais. O vNet peering apoia o peering de dois VNets, tanto na mesma região azure (vulgarmente chamado de vnet peering) como em duas regiões azure diferentes (vulgarmente chamados de peering Global VNet). 

Vamos configurar o Global VNet entre os VNets em assinaturas Contoso e Fabrikam Azure. Para como criar a rede virtual que espreita entre duas redes virtuais, consulte Criar um artigo de peering de [rede virtual.][Configure VNet peering]

A imagem seguinte mostra a arquitetura da rede depois de configurar o peering Global VNet.

[![6]][6]

A tabela que se segue mostra as rotas conhecidas pela VM de assinatura Contoso. Preste atenção à última entrada da mesa. Esta entrada é o resultado da ligação cruzada das redes virtuais.

[![7]][7]

A tabela que se segue mostra as rotas conhecidas pela VM de subscrição fabrikam. Preste atenção à última entrada da mesa. Esta entrada é o resultado da ligação cruzada das redes virtuais.

[![8]][8]

O vNet peering liga diretamente duas redes virtuais (ver que não há próximo salto para a entrada *VNetGlobalPeering* nas duas tabelas acima)

## <a name="cross-connecting-vnets-to-the-on-premises-networks"></a>Cross connecting VNets às redes no local

Podemos ligar um circuito ExpressRoute a várias redes virtuais. Consulte os limites de [subscrição e serviço][Subscription limits] para o número máximo de redes virtuais que podem ser ligadas a um circuito ExpressRoute. 

Vamos ligar o circuito Fabrikam ExpressRoute à assinatura Contoso VNet e similarmente o circuito Contoso ExpressRoute à Assinatura Fabrikam VNet para permitir a conectividade cruzada entre redes virtuais e as redes no local. Para ligar uma rede virtual a um circuito ExpressRoute numa subscrição diferente, precisamos de criar e usar uma autorização.  Ver o artigo: [Ligue uma rede virtual a um circuito ExpressRoute][Connect-ER-VNet].

A imagem seguinte mostra a arquitetura da rede depois de configurar a conectividade transversal ExpressRoute às redes virtuais.

[![9]][9]

A tabela seguinte mostra a tabela de rotas do peering privado da Rota Expresso de Contoso Ltd., após a ligação cruzada das redes virtuais às redes no local via ExpressRoute. Veja se a tabela de rotas tem rotas pertencentes a ambas as redes virtuais.

[![10]][10]

A tabela seguinte mostra a tabela de rotas do peering privado da Rota Expresso da Fabrikam Inc., depois de cruzar redes virtuais às redes no local via ExpressRoute. Veja se a tabela de rotas tem rotas pertencentes a ambas as redes virtuais.

[![11]][11]

A tabela que se segue mostra as rotas conhecidas pela VM de assinatura Contoso. Preste atenção às entradas de gateway da *rede Virtual* da tabela. O VM vê rotas para ambas as redes no local.

[![12]][12]

A tabela que se segue mostra as rotas conhecidas pela VM de subscrição fabrikam. Preste atenção às entradas de gateway da *rede Virtual* da tabela. O VM vê rotas para ambas as redes no local.

[![13]][13]

>[!NOTE]
>Nas subscrições Fabrikam e/ou Contoso, também pode ter falado VNets com o respetivo hub VNet (um hub e design falado não é ilustrado nos diagramas de arquitetura neste artigo). As ligações cruzadas entre os portões VNet do hub para a ExpressRoute também permitirão a comunicação entre os centros e porta-vozes do Leste e do Oeste.
>

## <a name="cross-connecting-on-premises-networks"></a>Redes de ligação cruzada no local

ExpressRoute Global Reach fornece conectividade entre redes no local que estão ligadas a diferentes circuitos ExpressRoute. Vamos configurar o Alcance Global entre os circuitos Contoso e Fabrikam ExpressRoute. Como os circuitos ExpressRoute estão em diferentes subscrições, precisamos de criar e usar uma autorização. Consulte o artigo [Configure ExpressRoute Global Reach][Configure Global Reach] para orientação passo a passo.

A imagem seguinte mostra a arquitetura da rede depois de configurar o Global Reach.

[![14]][14]

A tabela seguinte mostra a tabela de rotas do peering privado da Rota Expresso de Contoso Ltd., depois de configurar o Global Reach. Ver que a tabela de rotas tem rotas pertencentes a ambas as redes no local. 

[![15]][15]

A tabela seguinte mostra a tabela de rotas do olhar privado da Rota Expresso da Fabrikam Inc., depois de configurar o Global Reach. Ver que a tabela de rotas tem rotas pertencentes a ambas as redes no local.

[![16]][16]

## <a name="next-steps"></a>Passos seguintes

Consulte [as FAQ da rede virtual,][VNet-FAQ]para mais perguntas sobre VNet e VNet-peering. Consulte o [ExpressRoute FAQ][ER-FAQ] para mais questões sobre expressRoute e conectividade de rede virtual.

O Global Reach é lançado numa base país/região por país/região. Para ver se o Global Reach está disponível nos países/regiões que deseja, consulte [expressRoute Global Reach][Global Reach].

<!--Image References-->
[1]: ./media/cross-network-connectivity/premergerscenario.png "O cenário de candidatura"
[2]: ./media/cross-network-connectivity/contosoexr-rt-premerger.pngTabela de "rota seletiva Contoso ExpressRoute antes da fusão"
[3]: ./media/cross-network-connectivity/fabrikamexr-rt-premerger.pngTabela de "rota da Fabrikam ExpressRoute antes da fusão"
[4]: ./media/cross-network-connectivity/contosovm-routes-premerger.png "Rotas VM Contoso antes da fusão"
[5]: ./media/cross-network-connectivity/fabrikamvm-routes-premerger.png "rotas VM Fabrikam antes da fusão"
[6]: ./media/cross-network-connectivity/vnet-peering.png "A Arquitetura depois do vnet-peering"
[7]: ./media/cross-network-connectivity/contosovm-routes-peering.png "Rotas VM Contoso após peering VNet"
[8]: ./media/cross-network-connectivity/fabrikamvm-routes-peering.png "rotas VM Fabrikam após o peering VNet"
[9]: ./media/cross-network-connectivity/exr-x-connect.png "A Arquitetura após a ligação cruzada ExpressRoutes"
[10]: ./media/cross-network-connectivity/contosoexr-rt-xconnect.pngTabela de rota da "Rota Expresso de Contoso após ligação cruzada ExR e VNets"
[11]: ./media/cross-network-connectivity/fabrikamexr-rt-xconnect.pngTabela de "rota da Fabrikam ExpressRoute após ligação cruzada ExR e VNets"
[12]: ./media/cross-network-connectivity/contosovm-routes-xconnect.png "Rotas De VM Contoso após ligação cruzada ExR e VNets"
[13]: ./media/cross-network-connectivity/fabrikamvm-routes-xconnect.png "Rotas VM Fabrikam após ligação cruzada ExR e VNets"
[14]: ./media/cross-network-connectivity/globalreach.png "A Arquitetura depois de configurar o Alcance Global"
[15]: ./media/cross-network-connectivity/contosoexr-rt-gr.pngTabela de rota da "Rota Expresso de Contoso após Alcance Global"
[16]: ./media/cross-network-connectivity/fabrikamexr-rt-gr.pngTabela de "rota da Fabrikam ExpressRoute após alcance global"

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