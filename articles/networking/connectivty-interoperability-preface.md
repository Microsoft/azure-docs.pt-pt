---
title: 'Interoperabilidade nas funcionalidades de conectividade traseira do Azure: Configuração de teste / Microsoft Docs'
description: Este artigo descreve uma configuração de teste que pode usar para analisar a interoperabilidade entre o ExpressRoute, uma VPN site-to-site e o peering de rede virtual em Azure.
documentationcenter: na
services: networking
author: rambk
manager: tracsman
ms.service: virtual-network
ms.topic: article
ms.workload: infrastructure-services
ms.date: 10/18/2018
ms.author: rambala
ms.openlocfilehash: 0cbd4b620a03ed26e95679cf7cb1abef277a9471
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/27/2020
ms.locfileid: "74873800"
---
# <a name="interoperability-in-azure-back-end-connectivity-features-test-setup"></a>Interoperabilidade nas funcionalidades de conectividade traseira do Azure: Configuração de teste

Este artigo descreve uma configuração de teste que pode usar para analisar como os serviços de networking Azure interoperam ao nível do plano de controlo e do plano de dados. Vamos olhar brevemente para os componentes de rede Azure:

-   **Azure ExpressRoute**: Utilize o peering privado no Azure ExpressRoute para ligar diretamente os espaços IP privados da sua rede no local às suas implementações da Rede Virtual Azure. O ExpressRoute pode ajudá-lo a alcançar uma maior largura de banda e uma ligação privada. Muitos parceiros ecológicos ExpressRoute oferecem conectividade ExpressRoute com SLAs. Para saber mais sobre o ExpressRoute e para aprender a configurar a ExpressRoute, consulte [introdução ao ExpressRoute][ExpressRoute].
-   **VPN site-to-site**: Você pode usar O Gateway VPN Azure como um VPN site-to-site para ligar de forma segura uma rede no local ao Azure através da internet ou usando o ExpressRoute. Para aprender a configurar uma VPN site-to-site para ligar ao Azure, consulte [Configure VPN Gateway][VPN].
-   **VNet peering**: Use a rede virtual (VNet) peering para estabelecer conectividade entre VNets na Rede Virtual Azure. Para saber mais sobre o peering vNet, consulte o [tutorial no VNet peering][VNet].

## <a name="test-setup"></a>Configuração do teste

A figura que se segue ilustra a configuração do ensaio:

![1][1]

A peça central da configuração do teste é o hub VNet na Região Azure 1. O hub VNet está ligado a diferentes redes das seguintes formas:

-   O hub VNet está ligado ao VNet falado usando o peering VNet. O VNet falado tem acesso remoto a ambos os gateways no centro VNet.
-   O hub VNet está ligado ao ramo VNet utilizando VPN site-to-site. A conectividade utiliza o EBGP para trocar rotas.
-   O hub VNet está ligado à rede local Location 1, utilizando o peering privado ExpressRoute como o caminho principal. Utiliza a conectividade VPN do site-a-site como o caminho de reserva. No resto deste artigo, referimo-nos a este circuito ExpressRoute como ExpressRoute 1. Por padrão, os circuitos ExpressRoute proporcionam conectividade redundante para alta disponibilidade. No ExpressRoute 1, a subinterface do router secundário de borda do cliente (CE) que enfrenta o secundário Microsoft Enterprise Edge Router (MSEE) está desativada. Uma linha vermelha sobre a seta de linha dupla na figura anterior representa o subinterface do router CE desativado.
-   O hub VNet está ligado à rede local Local 2 utilizando outro peering privado ExpressRoute. No resto deste artigo, referimo-nos a este segundo circuito ExpressRoute como ExpressRoute 2.
-   ExpressRoute 1 também liga tanto o hub VNet como a rede local 1 a um VNet remoto na Região Azure 2.

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

Conheça os detalhes da [configuração][Configuration] para a topologia do teste.

Conheça a análise do plano de [controlo][Control-Analysis] da configuração do teste e as vistas de diferentes VNets ou VLANs na topologia.

Conheça a análise de [planos][Data-Analysis] de dados da configuração do teste e das vistas da funcionalidade de monitorização da rede Azure.

Consulte o [ExpressRoute FAQ][ExR-FAQ] para:
-   Saiba quantos circuitos ExpressRoute pode ligar a um gateway ExpressRoute.
-   Saiba quantos gateways ExpressRoute pode ligar a um circuito ExpressRoute.
-   Conheça outros limites de escala da ExpressRoute.


<!--Image References-->
[1]: ./media/backend-interoperability/TestSetup.png "Diagrama da topologia de teste"

<!--Link References-->
[ExpressRoute]: https://docs.microsoft.com/azure/expressroute/expressroute-introduction
[VPN]: https://docs.microsoft.com/azure/vpn-gateway/vpn-gateway-about-vpngateways
[VNet]: https://docs.microsoft.com/azure/virtual-network/tutorial-connect-virtual-networks-portal
[Configuration]: connectivty-interoperability-configuration.md
[Control-Analysis]: connectivty-interoperability-control-plane.md
[Data-Analysis]: connectivty-interoperability-data-plane.md
[ExR-FAQ]: https://docs.microsoft.com/azure/expressroute/expressroute-faqs
[S2S-Over-ExR]: https://docs.microsoft.com/azure/expressroute/site-to-site-vpn-over-microsoft-peering
[ExR-S2S-CoEx]: https://docs.microsoft.com/azure/expressroute/expressroute-howto-coexist-resource-manager
[Hub-n-Spoke]: https://docs.microsoft.com/azure/architecture/reference-architectures/hybrid-networking/hub-spoke
[Deploy-NVA]: https://docs.microsoft.com/azure/architecture/reference-architectures/dmz/nva-ha


