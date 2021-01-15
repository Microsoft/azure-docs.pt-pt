---
title: 'Interoperabilidade em Azure : Configuração de teste | Microsoft Docs'
description: Este artigo descreve uma configuração de teste que pode usar para analisar a interoperabilidade entre o ExpressRoute, uma VPN site-to-site e a rede virtual que espreita em Azure.
documentationcenter: na
services: networking
author: rambk
manager: tracsman
ms.service: virtual-network
ms.topic: article
ms.workload: infrastructure-services
ms.date: 10/18/2018
ms.author: rambala
ms.openlocfilehash: 206cc70b5ee636ec3cc54727c3e94a10ad2426d1
ms.sourcegitcommit: c7153bb48ce003a158e83a1174e1ee7e4b1a5461
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 01/15/2021
ms.locfileid: "98234142"
---
# <a name="interoperability-in-azure--test-setup"></a>Interoperabilidade em Azure : Configuração do teste

Este artigo descreve uma configuração de teste que pode usar para analisar como os serviços de networking Azure interoperam ao nível do plano de controlo e ao nível do plano de dados. Vejamos brevemente os componentes de rede Azure:

-   **Azure ExpressRoute**: Utilize o espreitamento privado no Azure ExpressRoute para ligar diretamente os espaços IP privados na sua rede de acesso às suas implementações da Rede Virtual Azure. O ExpressRoute pode ajudá-lo a alcançar uma largura de banda mais elevada e uma ligação privada. Muitos parceiros eco ExpressRoute oferecem conectividade ExpressRoute com SLAs. Para saber mais sobre o ExpressRoute e para aprender a configurar o ExpressRoute, consulte [Introdução ao ExpressRoute][ExpressRoute].
-   **VPN site-to-site**: Pode utilizar o Azure VPN Gateway como VPN site-to-site para ligar de forma segura uma rede no local ao Azure através da internet ou utilizando o ExpressRoute. Para aprender a configurar uma VPN site-to-site para ligar ao Azure, consulte [o Configure VPN Gateway][VPN].
-   **VNet peering**: Use rede virtual (VNet) olhando para estabelecer conectividade entre VNets na Rede Virtual Azure. Para saber mais sobre olhando vNet, consulte o [tutorial no VNet espreitando][VNet].

## <a name="test-setup"></a>Configuração do teste

A seguinte figura ilustra a configuração do teste:

![1][1]

A peça central da configuração do teste é o hub VNet na Região Azure 1. O hub VNet está ligado a diferentes redes das seguintes formas:

-   O hub VNet está ligado ao VNet falado utilizando o peering VNet. O VNet falado tem acesso remoto a ambos os gateways no hub VNet.
-   O hub VNet está ligado ao ramo VNet utilizando VPN site-to-site. A conectividade utiliza o eBGP para trocar rotas.
-   O hub VNet está ligado à rede localização 1 no local, utilizando o espreitamento privado ExpressRoute como o caminho principal. Utiliza a conectividade VPN site-to-site como o caminho de backup. No resto deste artigo, referimo-nos a este circuito ExpressRoute como ExpressRoute 1. Por padrão, os circuitos ExpressRoute proporcionam conectividade redundante para uma elevada disponibilidade. No ExpressRoute 1, o subinterface do router secundário (CE) que enfrenta o microsoft enterprise edge router secundário (MSEE) está desativado. Uma linha vermelha sobre a seta de linha dupla na figura anterior representa o subinterftra do router CE desativado.
-   O hub VNet está ligado à rede localização 2 no local, utilizando outro espreitamento privado ExpressRoute. No resto deste artigo, referimo-nos a este segundo circuito ExpressRoute como ExpressRoute 2.
-   O ExpressRoute 1 também liga tanto o hub VNet como a rede de localização 1 no local a um VNet remoto na Região de Azure 2.

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

Saiba mais detalhes de [configuração][Configuration] para a topologia do teste.

Saiba mais sobre a análise do plano de [controlo][Control-Analysis] da configuração do teste e as vistas de diferentes VNets ou VLANs na topologia.

Conheça a análise do plano de [dados][Data-Analysis] da configuração do teste e as vistas do recurso de monitorização da rede Azure.

Consulte as [FAQ expressRoute][ExR-FAQ] para:
-   Saiba quantos circuitos ExpressRoute pode ligar a um gateway ExpressRoute.
-   Saiba quantos gateways ExpressRoute pode ligar a um circuito ExpressRoute.
-   Saiba mais sobre outros limites de escala do ExpressRoute.


<!--Image References-->
[1]: ./media/backend-interoperability/TestSetup.png "Diagrama da topologia do teste"

<!--Link References-->
[ExpressRoute]: ../expressroute/expressroute-introduction.md
[VPN]: ../vpn-gateway/vpn-gateway-about-vpngateways.md
[VNet]: ../virtual-network/tutorial-connect-virtual-networks-portal.md
[Configuration]: connectivty-interoperability-configuration.md
[Control-Analysis]: connectivty-interoperability-control-plane.md
[Data-Analysis]: connectivty-interoperability-data-plane.md
[ExR-FAQ]: ../expressroute/expressroute-faqs.md
[S2S-Over-ExR]: ../expressroute/site-to-site-vpn-over-microsoft-peering.md
[ExR-S2S-CoEx]: ../expressroute/expressroute-howto-coexist-resource-manager.md
[Hub-n-Spoke]: /azure/architecture/reference-architectures/hybrid-networking/hub-spoke
[Deploy-NVA]: /azure/architecture/reference-architectures/dmz/nva-ha