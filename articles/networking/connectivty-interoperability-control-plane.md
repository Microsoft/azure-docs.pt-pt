---
title: 'Interoperabilidade em Azure : Controlo da análise do plano'
description: Este artigo fornece a análise do plano de controlo da configuração do teste que pode utilizar para analisar a interoperabilidade entre o ExpressRoute, uma VPN site-to-site, e o espreitamento de rede virtual em Azure.
documentationcenter: na
services: networking
author: rambk
manager: tracsman
ms.service: virtual-network
ms.topic: article
ms.workload: infrastructure-services
ms.date: 10/18/2018
ms.author: rambala
ms.openlocfilehash: 4dfd869b92e042e71eed1ee692d90fc44a8ac6c1
ms.sourcegitcommit: c7153bb48ce003a158e83a1174e1ee7e4b1a5461
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 01/15/2021
ms.locfileid: "98234244"
---
# <a name="interoperability-in-azure--control-plane-analysis"></a>Interoperabilidade em Azure : Controlo da análise do plano

Este artigo descreve a análise do plano de controlo da [configuração][Setup]do teste . Também pode rever a configuração da [configuração][Configuration] do teste e a análise do plano de [dados][Data-Analysis] da configuração do teste.

A análise do plano de controlo examina essencialmente as rotas que são trocadas entre redes dentro de uma topologia. A análise do plano de controlo pode ajudá-lo a entender como diferentes redes vêem a topologia.

## <a name="hub-and-spoke-vnet-perspective"></a>Hub e falou perspetiva VNet

A figura a seguir ilustra a rede na perspetiva de uma rede virtual de hub (VNet) e de um VNet falado (realçado em azul). O número mostra ainda o número de sistema autónomo (ASN) das diferentes redes e rotas que são trocadas entre diferentes redes: 

![1][1]

A ASN do gateway Azure ExpressRoute da VNet é diferente da ASN dos Microsoft Enterprise Edge Routers (MSEEs). Um gateway ExpressRoute utiliza uma ASN privada (um valor de **65515**) e os MSEEs utilizam o PÚBLICO ASN (um valor de **12076**) globalmente. Quando configura o ExpressRoute, porque o MSEE é o par, usa **12076** como par ASN. Do lado Azure, o MSEE estabelece o eBGP olhando com o gateway ExpressRoute. O duplo eBGP que o MSEE estabelece para cada observação ExpressRoute é transparente ao nível do plano de controlo. Portanto, quando vê uma tabela de rotas ExpressRoute, vê o gateway ExpressRoute ASN da VNet para os prefixos do VNet. 

O seguinte número mostra uma amostra do quadro de rota expressRoute: 

![5][5]

Dentro de Azure, a ASN é significativa apenas do ponto de vista do espreitar. Por padrão, a ASN do gateway ExpressRoute e da porta de entrada VPN em Azure VPN Gateway é **65515**.

## <a name="on-premises-location-1-and-the-remote-vnet-perspective-via-expressroute-1"></a>Local 1 e a perspetiva remota do VNet via ExpressRoute 1

Tanto no local, a localização 1 como a VNet remota estão ligadas ao hub VNet via ExpressRoute 1. Partilham a mesma perspetiva da topologia, como mostra o seguinte diagrama:

![2][2]

## <a name="on-premises-location-1-and-the-branch-vnet-perspective-via-a-site-to-site-vpn"></a>Local 1 e a perspetiva VNet do ramo através de uma VPN local-local

Tanto no local, a localização 1 como a sucursal VNet estão ligadas ao gateway VPN de um hub VNet através de uma ligação VPN local-local. Partilham a mesma perspetiva da topologia, como mostra o seguinte diagrama:

![3][3]

## <a name="on-premises-location-2-perspective"></a>Perspetiva de localização no local 2

No local A localização 2 está ligada a um hub VNet através de um perspero privado do ExpressRoute 2: 

![4][4]

## <a name="expressroute-and-site-to-site-vpn-connectivity-in-tandem"></a>ExpressRoute e conectividade VPN site-to-site em conjunto

###  <a name="site-to-site-vpn-over-expressroute"></a>VPN site-to-site sobre ExpressRoute

Pode configurar uma VPN site-to-site utilizando o ExpressRoute Microsoft olhando para trocar dados privados entre a sua rede no local e os seus VNets Azure. Com esta configuração, pode trocar dados com confidencialidade, autenticidade e integridade. A troca de dados também é anti-repetição. Para obter mais informações sobre como configurar um IPsec VPN site-to-site no modo túnel utilizando o olho da Microsoft ExpressRoute, consulte [o VPN site-to-site sobre o expressRoute Microsoft olhando][S2S-Over-ExR]. 

A limitação primária de configurar uma VPN site-to-site que utiliza o esprevamento da Microsoft é a produção. A entrada sobre o túnel IPsec é limitada pela capacidade de gateway VPN. A entrada de gateway VPN é inferior à produção expressRoute. Neste cenário, a utilização do túnel IPsec para tráfego altamente seguro e a utilização de um espremia privado para todos os outros tráfegos ajuda a otimizar a utilização da largura de banda ExpressRoute.

### <a name="site-to-site-vpn-as-a-secure-failover-path-for-expressroute"></a>VPN site-to-site como um caminho de falha seguro para ExpressRoute

O ExpressRoute serve como um par de circuitos redundantes para garantir uma elevada disponibilidade. Pode configurar a conectividade ExpressRoute geo-redundante em diferentes regiões do Azure. Além disso, como demonstrado na nossa configuração de teste, dentro de uma região de Azure, você pode usar uma VPN site-to-site para criar um caminho de failover para a sua conectividade ExpressRoute. Quando os mesmos prefixos são anunciados tanto sobre o ExpressRoute como sobre uma VPN site-to-site, a Azure prioriza o ExpressRoute. Para evitar o encaminhamento assimétrico entre o ExpressRoute e a VPN local-a-local, a configuração da rede no local também deve retribuir utilizando a conectividade ExpressRoute antes de utilizar a conectividade VPN site-to-site.

Para obter mais informações sobre como configurar as ligações coexistindo para o ExpressRoute e uma VPN local-a-local, consulte [o ExpressRoute e a coexistência local- local.][ExR-S2S-CoEx]

## <a name="extend-back-end-connectivity-to-spoke-vnets-and-branch-locations"></a>Alargar a conectividade de back-end a VNets falados e localizações de filiais

### <a name="spoke-vnet-connectivity-by-using-vnet-peering"></a>Conectividade falada VNet usando o peering VNet

Hub e a arquitetura VNet falada é amplamente usada. O hub é um VNet em Azure que funciona como um ponto central de conectividade entre os seus VNets falados e a sua rede no local. Os porta-vozes são VNets que se juntam ao hub, e que você pode usar para isolar cargas de trabalho. O tráfego flui entre o datacenter no local e o hub através de uma ligação ExpressRoute ou VPN. Para obter mais informações sobre a arquitetura, consulte [Implementar uma topologia de rede de eixos em Azure.][Hub-n-Spoke]

Em VNet espreitando dentro de uma região, os VNets falados podem usar gateways hub VNet (ambos gateways VPN e ExpressRoute) para comunicar com redes remotas.

### <a name="branch-vnet-connectivity-by-using-site-to-site-vpn"></a>Conectividade branch VNet utilizando VPN site-to-site

Você pode querer VNets de ramo, que estão em diferentes regiões, e redes no local para comunicar uns com os outros através de um hub VNet. A solução Azure nativa para esta configuração é a conectividade VPN local-a-local utilizando uma VPN. Uma alternativa é utilizar um aparelho virtual de rede (NVA) para encaminhamento no centro.

Para mais informações, veja [o que é VPN Gateway e][VPN] [implemente um NVA altamente disponível][Deploy-NVA].

## <a name="next-steps"></a>Passos seguintes

Saiba mais sobre a análise do plano de [dados][Data-Analysis] da configuração do teste e as vistas do recurso de monitorização da rede Azure.

Consulte as [FAQ expressRoute][ExR-FAQ] para:
-   Saiba quantos circuitos ExpressRoute pode ligar a um gateway ExpressRoute.
-   Saiba quantos gateways ExpressRoute pode ligar a um circuito ExpressRoute.
-   Saiba mais sobre outros limites de escala do ExpressRoute.


<!--Image References-->
[1]: ./media/backend-interoperability/HubView.png "Hub e falou perspetiva VNet da topologia"
[2]: ./media/backend-interoperability/Loc1ExRView.png "Localização 1 e perspetiva remota de VNet da topologia via ExpressRoute 1"
[3]: ./media/backend-interoperability/Loc1VPNView.png "Localização 1 e perspetiva VNet do ramo da topologia através de uma VPN site-to-site"
[4]: ./media/backend-interoperability/Loc2View.png "Localização 2 perspetiva da topologia"
[5]: ./media/backend-interoperability/ExR1-RouteTable.png "Tabela de rotas ExpressRoute 1"

<!--Link References-->
[Setup]: ./connectivty-interoperability-preface.md
[Configuration]: ./connectivty-interoperability-configuration.md
[ExpressRoute]: ../expressroute/expressroute-introduction.md
[VPN]: ../vpn-gateway/vpn-gateway-about-vpngateways.md
[VNet]: ../virtual-network/tutorial-connect-virtual-networks-portal.md
[Configuration]: ./connectivty-interoperability-configuration.md
[Control-Analysis]:https://docs.microsoft.com/azure/networking/connectivty-interoperability-control-plane
[Data-Analysis]: ./connectivty-interoperability-data-plane.md
[ExR-FAQ]: ../expressroute/expressroute-faqs.md
[S2S-Over-ExR]: ../expressroute/site-to-site-vpn-over-microsoft-peering.md
[ExR-S2S-CoEx]: ../expressroute/expressroute-howto-coexist-resource-manager.md
[Hub-n-Spoke]: /azure/architecture/reference-architectures/hybrid-networking/hub-spoke
[Deploy-NVA]: /azure/architecture/reference-architectures/dmz/nva-ha
[VNet-Config]: ../virtual-network/virtual-network-manage-peering.md