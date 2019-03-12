---
title: 'Interoperabilidade nas funcionalidades de conectividade de back-end do Azure: Configuração de teste | Documentos da Microsoft'
description: Este artigo descreve uma configuração de teste, que pode usar para analisar a interoperabilidade entre o ExpressRoute, uma VPN site a site e da rede virtual peering no Azure.
documentationcenter: na
services: networking
author: rambk
manager: tracsman
ms.service: virtual-network
ms.topic: article
ms.workload: infrastructure-services
ms.date: 10/18/2018
ms.author: rambala
ms.openlocfilehash: 8be546c5dba4c6c694c8cef03a4bdd6005d68189
ms.sourcegitcommit: 5fbca3354f47d936e46582e76ff49b77a989f299
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/12/2019
ms.locfileid: "57771570"
---
# <a name="interoperability-in-azure-back-end-connectivity-features-test-setup"></a>Interoperabilidade nas funcionalidades de conectividade de back-end do Azure: Configuração de teste

Este artigo descreve um programa de configuração de teste, que pode usar para analisar como o Azure serviços de rede interoperate ao nível de plano de controlo do e nível de plano de dados. Vamos analisar resumidamente os componentes de rede do Azure:

-   **O Azure ExpressRoute**: Utilize o peering privado no Azure ExpressRoute para ligar diretamente os espaços IP privados na sua rede no local para as implementações de rede Virtual do Azure. ExpressRoute pode ajudá-lo a alcançar maior largura de banda e uma ligação privada. Muitos parceiros do ExpressRoute ao meio ambiente oferecem conectividade do ExpressRoute com SLAs. Para saber mais sobre o ExpressRoute e para saber como configurar o ExpressRoute, veja [introdução ao ExpressRoute][ExpressRoute].
-   **Site-to-site VPN**: Pode utilizar o Gateway de VPN do Azure como uma VPN de site a site para ligar em segurança uma rede no local para o Azure através da internet ou com o ExpressRoute. Para saber como configurar uma VPN de site a site para ligar ao Azure, veja [configurar o Gateway de VPN][VPN].
-   **O VNet peering**: Utilize o peering de rede virtual (VNet) para estabelecer conectividade entre VNets na rede Virtual do Azure. Para saber mais sobre VNet peering, veja a [tutorial sobre VNet peering][VNet].

## <a name="test-setup"></a>Configuração de teste

A figura seguinte ilustra a configuração de teste:

[![1]][1]

O ponto central da configuração do teste é a VNet no Azure 1 da região do hub. A VNet do hub está ligado a redes diferentes das seguintes formas:

-   A VNet do hub está ligado para o spoke VNet ao utilizar o VNet peering. O VNet spoke tem acesso remoto para ambos os gateways na VNet do hub.
-   A VNet do hub está ligado para o ramo VNet através da VPN site a site. A conectividade utiliza eBGP para trocar as rotas.
-   A VNet do hub está ligado à rede de 1 de localização no local com o peering privado do ExpressRoute como o caminho principal. Ele usa a conectividade VPN de site a site como o caminho de cópia de segurança. No restante deste artigo, fazemos referência a este circuito do ExpressRoute como 1 do ExpressRoute. Por predefinição, os circuitos do ExpressRoute fornecem conectividade redundante para elevada disponibilidade. Em 1 do ExpressRoute, está desativada a subinterface o router de limite (CE) de cliente secundário que enfrenta do Microsoft Enterprise Edge Router (MSEE secundário). Uma linha vermelha por cima da seta dupla Linha na figura anterior representa a subinterface de router CE desativada.
-   A VNet do hub está ligado à rede de 2 de localização no local através de outro peering privado do ExpressRoute. No restante deste artigo, fazemos referência a este circuito do ExpressRoute segundo como 2 do ExpressRoute.
-   1 do ExpressRoute também se conecta a VNet do hub e a rede de 1 de localização no local a uma VNet remota em 2 de região do Azure.

## <a name="expressroute-and-site-to-site-vpn-connectivity-in-tandem"></a>Conectividade VPN de site a site e ExpressRoute em conjunto

###  <a name="site-to-site-vpn-over-expressroute"></a>VPN de site a site através do ExpressRoute

Pode configurar uma VPN de site a site com o ExpressRoute peering em privado trocar dados entre a rede no local e as VNets do Azure da Microsoft. Com esta configuração, podem trocar dados com confidencialidade e integridade autenticidade. Além disso, a troca de dados é anti-repetições. Para obter mais informações sobre como configurar uma VPN IPsec de site a site no modo de túnel com o ExpressRoute peering da Microsoft, consulte [VPN de Site a site através do peering da Microsoft do ExpressRoute][S2S-Over-ExR]. 

A principal limitação de configuração de uma VPN de site a site que utiliza o peering da Microsoft é a taxa de transferência. Taxa de transferência sobre o túnel IPsec é limitada pela capacidade do gateway VPN. O débito do gateway VPN é inferior a taxa de transferência do ExpressRoute. Neste cenário, utilizar o túnel IPsec para o tráfego altamente seguro e utilizar o peering privado para todos os outros tráfegos ajuda a otimizar a utilização de largura de banda do ExpressRoute.

### <a name="site-to-site-vpn-as-a-secure-failover-path-for-expressroute"></a>VPN de site a site como um caminho de ativação pós-falha seguro para o ExpressRoute

ExpressRoute funciona como um par de circuito redundantes para elevada disponibilidade. Pode configurar a conectividade do ExpressRoute com redundância geográfica em diferentes regiões do Azure. Além disso, como demonstrado em nossa configuração de teste, dentro de uma região do Azure, pode utilizar uma VPN de site a site para criar um caminho de ativação pós-falha para a conectividade do ExpressRoute. Quando os mesmos prefixos são anunciados através do ExpressRoute e uma VPN de site a site, o Azure dá prioridade ExpressRoute. Para evitar o encaminhamento assimétricas entre ExpressRoute e VPN site a site, no local a configuração de rede também deve reciprocate utilizando a conectividade do ExpressRoute antes de ele usa a conectividade VPN de site a site.

Para obter mais informações sobre como configurar ligações coexistentes ExpressRoute e uma VPN de site a site, consulte [ExpressRoute e coexistência site a site][ExR-S2S-CoEx].

## <a name="extend-back-end-connectivity-to-spoke-vnets-and-branch-locations"></a>Expandir a conectividade de back-end para spoke VNets e localizações de sucursais

### <a name="spoke-vnet-connectivity-by-using-vnet-peering"></a>Conectividade de VNet do spoke ao utilizar o VNet peering

Hub- and -spoke arquitetura da VNet é amplamente utilizada. O hub é uma VNet no Azure que age como um ponto central de conectividade entre a VNets spoke e à sua rede no local. Os spokes são VNets que partilham com o hub, e que pode ser usada para isolar cargas de trabalho. O tráfego flui entre o datacenter no local e o hub através de uma ligação VPN ou ExpressRoute. Para obter mais informações sobre a arquitetura, consulte [implementar uma topologia de rede hub-and-spoke no Azure][Hub-n-Spoke].

O VNet peering dentro de uma região, spoke VNets pode utilizar gateways de VNet do hub (gateways VPN e ExpressRoute) para comunicar com redes remotas.

### <a name="branch-vnet-connectivity-by-using-site-to-site-vpn"></a>Ramificar a conectividade VNet através da VPN site a site

Pode desejar ramo VNets, que estão em diferentes regiões e redes no local para comunicar entre si através de uma VNet do hub. A solução nativa do Azure para esta configuração é a conectividade VPN de site a site com uma VPN. Uma alternativa é usar uma aplicação virtual de rede (NVA) para o encaminhamento no hub.

Para obter mais informações, consulte [o que é o Gateway de VPN?] [ VPN] e [implementar uma NVA altamente disponível][Deploy-NVA].

## <a name="next-steps"></a>Passos Seguintes

Saiba mais sobre [detalhes de configuração] [ Configuration] para a topologia de teste.

Saiba mais sobre [controlar a análise de plano] [ Control-Analysis] da configuração de teste e as vistas de VNets de diferentes ou VLANs na topologia.

Saiba mais sobre o [análise do plano de dados] [ Data-Analysis] da configuração de teste e vistas de funcionalidade de monitorização de rede do Azure.

Consulte a [FAQ do ExpressRoute] [ ExR-FAQ] para:
-   Saiba quantos circuitos do ExpressRoute pode ligar a um gateway do ExpressRoute.
-   Saiba quantos gateways do ExpressRoute pode ligar a um circuito do ExpressRoute.
-   Saiba mais sobre outros limites de dimensionamento do ExpressRoute.


<!--Image References-->
[1]: ./media/backend-interoperability/TestSetup.png "diagrama da topologia de teste"

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


