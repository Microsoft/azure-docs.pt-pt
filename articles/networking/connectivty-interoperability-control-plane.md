---
title: 'Interoperabilidade em Azure : Controlar a análise de planos'
description: Este artigo fornece a análise do plano de controlo da configuração do teste que pode utilizar para analisar a interoperabilidade entre o ExpressRoute, uma VPN site-to-site e o peering de rede virtual em Azure.
documentationcenter: na
services: networking
author: rambk
manager: tracsman
ms.service: virtual-network
ms.topic: article
ms.workload: infrastructure-services
ms.date: 10/18/2018
ms.author: rambala
ms.openlocfilehash: 5e41bc86533815c394077bf5276d930fe958cd19
ms.sourcegitcommit: b0ff9c9d760a0426fd1226b909ab943e13ade330
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/01/2020
ms.locfileid: "80518276"
---
# <a name="interoperability-in-azure--control-plane-analysis"></a>Interoperabilidade em Azure : Controlar a análise de planos

Este artigo descreve a análise do plano de controlo da [configuração][Setup]do teste . Também pode rever a configuração da [configuração][Configuration] do teste e a análise do plano de [dados][Data-Analysis] da configuração do teste.

Controle a análise de planos essencialmente examina rotas que são trocadas entre redes dentro de uma topologia. Controlar a análise de planos pode ajudá-lo a entender como diferentes redes vêem a topologia.

## <a name="hub-and-spoke-vnet-perspective"></a>Hub e perspetiva VNet falada

A figura que se segue ilustra a rede a partir da perspetiva de uma rede virtual hub (VNet) e de um VNet falado (realçado em azul). O número mostra também o número de sistema autónomo (ASN) de diferentes redes e rotas que são trocadas entre diferentes redes: 

![1][1]

O ASN do gateway Azure ExpressRoute da VNet é diferente do ASN dos Routers Microsoft Enterprise Edge (MSEEs). Um gateway ExpressRoute utiliza uma ASN privada (um valor de **65515)** e os MSEEs usam as N (um valor de **12076)** a nível global. Quando configurao o peering ExpressRoute, porque o MSEE é o par, utiliza-se **12076** como par ASN. Do lado do Azure, o MSEE estabelece o eBGP a espreitar com o gateway ExpressRoute. O duplo eBGP que o MSEE estabelece para cada peering ExpressRoute é transparente ao nível do plano de controlo. Portanto, ao ver uma tabela de rotas ExpressRoute, vê o gateway ASN da VNet para os prefixos da VNet. 

O seguinte número mostra uma tabela de rota expressRoute da amostra: 

![5][5]

Dentro do Azure, a ASN é significativa apenas de uma perspetiva de observação. Por predefinição, a ASN tanto da gateway ExpressRoute como da porta vpn em Azure VPN Gateway é **65515**.

## <a name="on-premises-location-1-and-the-remote-vnet-perspective-via-expressroute-1"></a>No local Localização 1 e a perspetiva vNet remota via ExpressRoute 1

Tanto no local A localização 1 como a VNet remota estão ligadas ao hub VNet via ExpressRoute 1. Partilham a mesma perspetiva da topologia, como mostra o seguinte diagrama:

![2][2]

## <a name="on-premises-location-1-and-the-branch-vnet-perspective-via-a-site-to-site-vpn"></a>No local Localização 1 e a perspetiva vNet do ramo através de uma VPN site-to-site

Tanto no local A Localização 1 como a vNet do ramo estão ligadas ao gateway VPN de um hub VNet através de uma ligação VPN site-to-site. Partilham a mesma perspetiva da topologia, como mostra o seguinte diagrama:

![3][3]

## <a name="on-premises-location-2-perspective"></a>Perspetiva de localização 2 no local

O local Local 2 está ligado a um hub VNet através de um epeering privado da ExpressRoute 2: 

![4][4]

## <a name="expressroute-and-site-to-site-vpn-connectivity-in-tandem"></a>ExpressRoute e conectividade VPN site-to-site em conjunto

###  <a name="site-to-site-vpn-over-expressroute"></a>VPN site-to-site sobre ExpressRoute

Pode configurar um VPN site-to-site utilizando o ExpressRoute Microsoft, olhando para a troca privada de dados entre a sua rede no local e os seus VNets Azure. Com esta configuração, pode trocar dados com confidencialidade, autenticidade e integridade. A troca de dados também é anti-repetição. Para obter mais informações sobre como configurar um IPsec VPN site-to-site em modo túnel, utilizando o peering expressRoute Microsoft, consulte [o Site-to-site VPN através do peering expressRoute Microsoft][S2S-Over-ExR]. 

A principal limitação de configurar uma VPN site-to-site que usa o peering da Microsoft é a entrada. A entrada sobre o túnel IPsec é limitada pela capacidade de gateway VPN. A entrada de gateway VPN é inferior à entrada do ExpressRoute. Neste cenário, a utilização do túnel IPsec para tráfego altamente seguro e a utilização de um esparso privado para todos os outros tráfegos ajuda a otimizar a utilização da largura de banda ExpressRoute.

### <a name="site-to-site-vpn-as-a-secure-failover-path-for-expressroute"></a>VPN do site-para-site como um caminho de falha seguro para expressRoute

A ExpressRoute serve como um par de circuitos redundantes para garantir uma alta disponibilidade. Pode configurar a conectividade ExpressRoute geo-redundante em diferentes regiões do Azure. Além disso, como demonstrado na nossa configuração de teste, dentro de uma região do Azure, você pode usar um VPN site-to-site para criar um caminho de failover para a sua conectividade ExpressRoute. Quando os mesmos prefixos são anunciados tanto sobre o ExpressRoute como sobre uma VPN site-to-site, o Azure prioriza o ExpressRoute. Para evitar o encaminhamento assimétrico entre o ExpressRoute e o VPN site-to-site, a configuração da rede no local também deve retribuir usando a conectividade ExpressRoute antes de utilizar a conectividade VPN site-to-site.

Para obter mais informações sobre como configurar as ligações coexistentes para o ExpressRoute e uma VPN site-to-site, consulte [expressRoute e coexistência site-to-site][ExR-S2S-CoEx].

## <a name="extend-back-end-connectivity-to-spoke-vnets-and-branch-locations"></a>Alargar a conectividade de back-end a VNets falados e localizações de filiais

### <a name="spoke-vnet-connectivity-by-using-vnet-peering"></a>Conectividade VNet falada usando o peering VNet

A arquitetura VNet hub e falada é amplamente usada. O hub é um VNet em Azure que funciona como um ponto central de conectividade entre os seus VNets falados e a sua rede no local. Os raios são VNets que espreitam com o centro, e que você pode usar para isolar cargas de trabalho. Fluxos de tráfego entre o datacenter no local e o centro através de uma ligação ExpressRoute ou VPN. Para obter mais informações sobre a arquitetura, consulte [Implementar uma topologia de rede de hub-spoke em Azure][Hub-n-Spoke].

Em VNet espreitando dentro de uma região, vNets falados podem usar gateways vNet hub (tanto gateways VPN como ExpressRoute) para comunicar com redes remotas.

### <a name="branch-vnet-connectivity-by-using-site-to-site-vpn"></a>Conectividade Branch VNet utilizando VPN site-to-site

Você pode querer que os VNets de ramificação, que estão em diferentes regiões, e redes no local para comunicar uns com os outros através de um hub VNet. A solução azure nativa para esta configuração é a conectividade VPN site-to-site utilizando uma VPN. Uma alternativa é utilizar um aparelho virtual de rede (NVA) para o encaminhamento no centro.

Para mais informações, consulte o [Deploy a highly available NVA][Deploy-NVA] [que é VPN Gateway?][VPN]

## <a name="next-steps"></a>Passos seguintes

Conheça a análise de [planos][Data-Analysis] de dados da configuração do teste e das vistas da funcionalidade de monitorização da rede Azure.

Consulte o [ExpressRoute FAQ][ExR-FAQ] para:
-   Saiba quantos circuitos ExpressRoute pode ligar a um gateway ExpressRoute.
-   Saiba quantos gateways ExpressRoute pode ligar a um circuito ExpressRoute.
-   Conheça outros limites de escala da ExpressRoute.


<!--Image References-->
[1]: ./media/backend-interoperability/HubView.png "Hub e falou perspetiva VNet da topologia"
[2]: ./media/backend-interoperability/Loc1ExRView.png "Localização 1 e perspetiva vNet remota da topologia via ExpressRoute 1"
[3]: ./media/backend-interoperability/Loc1VPNView.png "Localização 1 e perspetiva vNet da topologia através de uma VPN site-a-site"
[4]: ./media/backend-interoperability/Loc2View.png "Localização 2 perspetiva da topologia"
[5]: ./media/backend-interoperability/ExR1-RouteTable.png "Tabela de rota expressRoute 1"

<!--Link References-->
[Setup]: https://docs.microsoft.com/azure/networking/connectivty-interoperability-preface
[Configuration]: https://docs.microsoft.com/azure/networking/connectivty-interoperability-configuration
[ExpressRoute]: https://docs.microsoft.com/azure/expressroute/expressroute-introduction
[VPN]: https://docs.microsoft.com/azure/vpn-gateway/vpn-gateway-about-vpngateways
[VNet]: https://docs.microsoft.com/azure/virtual-network/tutorial-connect-virtual-networks-portal
[Configuration]: https://docs.microsoft.com/azure/networking/connectivty-interoperability-configuration
[Control-Analysis]:https://docs.microsoft.com/azure/networking/connectivty-interoperability-control-plane
[Data-Analysis]: https://docs.microsoft.com/azure/networking/connectivty-interoperability-data-plane
[ExR-FAQ]: https://docs.microsoft.com/azure/expressroute/expressroute-faqs
[S2S-Over-ExR]: https://docs.microsoft.com/azure/expressroute/site-to-site-vpn-over-microsoft-peering
[ExR-S2S-CoEx]: https://docs.microsoft.com/azure/expressroute/expressroute-howto-coexist-resource-manager
[Hub-n-Spoke]: https://docs.microsoft.com/azure/architecture/reference-architectures/hybrid-networking/hub-spoke
[Deploy-NVA]: https://docs.microsoft.com/azure/architecture/reference-architectures/dmz/nva-ha
[VNet-Config]: https://docs.microsoft.com/azure/virtual-network/virtual-network-manage-peering


