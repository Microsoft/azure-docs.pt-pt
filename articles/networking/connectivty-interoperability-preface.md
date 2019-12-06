---
title: 'Interoperabilidade nos recursos de conectividade de back-end do Azure: configuração de teste | Microsoft Docs'
description: Este artigo descreve uma configuração de teste que você pode usar para analisar a interoperabilidade entre o ExpressRoute, uma VPN site a site e um emparelhamento de rede virtual no Azure.
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
ms.sourcegitcommit: 9405aad7e39efbd8fef6d0a3c8988c6bf8de94eb
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 12/05/2019
ms.locfileid: "74873800"
---
# <a name="interoperability-in-azure-back-end-connectivity-features-test-setup"></a>Interoperabilidade nos recursos de conectividade de back-end do Azure: configuração de teste

Este artigo descreve uma configuração de teste que você pode usar para analisar como os serviços de rede do Azure interoperam nos níveis de plano de controle e plano de dados. Vamos examinar brevemente os componentes de rede do Azure:

-   **Azure expressroute**: Use o emparelhamento privado no Azure expressroute para conectar diretamente espaços IP privados em sua rede local às implantações de rede virtual do Azure. O ExpressRoute pode ajudá-lo a obter uma maior largura de banda e uma conexão privada. Muitos parceiros de ExpressRoute de rota expressa oferecem conectividade de ExpressRoute com SLAs. Para saber mais sobre o ExpressRoute e saber como configurar o ExpressRoute, consulte [introdução ao expressroute][ExpressRoute].
-   **VPN site a site**: você pode usar o gateway de VPN do Azure como uma VPN site a site para conectar com segurança uma rede local ao Azure pela Internet ou usando o ExpressRoute. Para saber como configurar uma VPN site a site para se conectar ao Azure, consulte Configurar o [Gateway de VPN][VPN].
-   **Emparelhamento vnet**: Use o emparelhamento de rede virtual (VNet) para estabelecer a conectividade entre o VNets na rede virtual do Azure. Para saber mais sobre o emparelhamento VNet, consulte o [tutorial sobre emparelhamento vnet][VNet].

## <a name="test-setup"></a>Configuração de teste

A figura a seguir ilustra a configuração do teste:

![1][1]

O ponto central da configuração do teste é a VNet do Hub na região 1 do Azure. A VNet do Hub está conectada a redes diferentes das seguintes maneiras:

-   A VNet do Hub está conectada à VNet do spoke usando o emparelhamento VNet. A VNet spoke tem acesso remoto a ambos os gateways na VNet do Hub.
-   A VNet do Hub está conectada à VNet do Branch usando VPN site a site. A conectividade usa eBGP para trocar rotas.
-   A VNet do Hub está conectada à rede local 1 usando o emparelhamento privado do ExpressRoute como o caminho principal. Ele usa a conectividade VPN site a site como o caminho de backup. No restante deste artigo, nos referimos a este circuito do ExpressRoute como ExpressRoute 1. Por padrão, os circuitos de ExpressRoute fornecem conectividade redundante para alta disponibilidade. No ExpressRoute 1, a subinterface do roteador da borda do cliente secundário (CE) que faces o roteador do Microsoft Enterprise Edge (MSEE) secundário está desabilitada. Uma linha vermelha na seta de linha dupla na figura anterior representa a subinterface do roteador CE desabilitada.
-   A VNet do Hub está conectada à rede local 2 usando outro emparelhamento privado do ExpressRoute. No restante deste artigo, nos referimos a esse segundo circuito do ExpressRoute como ExpressRoute 2.
-   O ExpressRoute 1 também conecta a VNet do Hub e a rede local 1 a uma VNet remota na região 2 do Azure.

## <a name="expressroute-and-site-to-site-vpn-connectivity-in-tandem"></a>Conectividade de VPN site a site e de ExpressRoute em tandem

###  <a name="site-to-site-vpn-over-expressroute"></a>VPN site a site por meio do ExpressRoute

Você pode configurar uma VPN site a site usando o emparelhamento da Microsoft ExpressRoute para trocar dados de forma privada entre sua rede local e o VNets do Azure. Com essa configuração, você pode trocar dados com confidencialidade, autenticidade e integridade. A troca de dados também é a anti-repetição. Para obter mais informações sobre como configurar uma VPN IPsec site a site no modo de túnel usando o emparelhamento da Microsoft do ExpressRoute, consulte [VPN site a site sobre o emparelhamento da Microsoft do expressroute][S2S-Over-ExR]. 

A principal limitação de configurar uma VPN site a site que usa o emparelhamento da Microsoft é a taxa de transferência. A taxa de transferência sobre o túnel IPsec é limitada pela capacidade do gateway de VPN. A taxa de transferência do gateway de VPN é inferior à taxa de transferência do ExpressRoute. Nesse cenário, o uso do túnel IPsec para tráfego altamente seguro e o uso de emparelhamento privado para todo o outro tráfego ajuda a otimizar a utilização da largura de banda do ExpressRoute.

### <a name="site-to-site-vpn-as-a-secure-failover-path-for-expressroute"></a>VPN site a site como um caminho de failover seguro para o ExpressRoute

O ExpressRoute serve como um par de circuitos redundantes para garantir a alta disponibilidade. Você pode configurar a conectividade do ExpressRoute com redundância geográfica em diferentes regiões do Azure. Além disso, conforme demonstrado em nossa configuração de teste, em uma região do Azure, você pode usar uma VPN site a site para criar um caminho de failover para a conectividade do ExpressRoute. Quando os mesmos prefixos são anunciados no ExpressRoute e em uma VPN site a site, o Azure prioriza o ExpressRoute. Para evitar o roteamento assimétrico entre o ExpressRoute e a VPN site a site, a configuração de rede local também deve ser reciprocada usando a conectividade de ExpressRoute antes de usar a conectividade VPN site a site.

Para obter mais informações sobre como configurar conexões coexistentes para o ExpressRoute e uma VPN site a site, consulte [ExpressRoute e coexistência site a site][ExR-S2S-CoEx].

## <a name="extend-back-end-connectivity-to-spoke-vnets-and-branch-locations"></a>Estender a conectividade de back-end para spoke VNets e locais de filiais

### <a name="spoke-vnet-connectivity-by-using-vnet-peering"></a>Conectividade de VNet do spoke usando o emparelhamento VNet

A arquitetura de VNet de Hub e spoke é amplamente usada. O Hub é uma VNet no Azure que atua como um ponto central de conectividade entre seu VNets de spoke e sua rede local. Os spokes são VNets que emparelham com o Hub e que você pode usar para isolar cargas de trabalho. O tráfego flui entre o datacenter local e o Hub por meio de uma conexão de ExpressRoute ou VPN. Para obter mais informações sobre a arquitetura, consulte [implementar uma topologia de rede hub-spoke no Azure][Hub-n-Spoke].

No emparelhamento VNet dentro de uma região, o spoke VNets pode usar gateways de VNet de Hub (gateways VPN e ExpressRoute) para se comunicar com redes remotas.

### <a name="branch-vnet-connectivity-by-using-site-to-site-vpn"></a>Conectividade de VNet de ramificação usando VPN site a site

Talvez você queira ramificar VNets, que estão em regiões diferentes, e redes locais para se comunicarem entre si por meio de uma VNet de Hub. A solução nativa do Azure para essa configuração é a conectividade VPN site a site usando uma VPN. Uma alternativa é usar uma NVA (solução de virtualização de rede) para roteamento no Hub.

Para obter mais informações, consulte [o que é o gateway de VPN?][VPN] e [implantar um NVA altamente disponível][Deploy-NVA].

## <a name="next-steps"></a>Passos seguintes

Saiba mais sobre os [detalhes de configuração][Configuration] para a topologia de teste.

Saiba mais sobre a [análise do plano de controle][Control-Analysis] da configuração do teste e as exibições de diferentes VNETS ou VLANs na topologia.

Saiba mais sobre a [análise do plano de dados][Data-Analysis] das exibições de configuração de teste e de monitoramento de rede do Azure.

Consulte as [perguntas frequentes sobre o ExpressRoute][ExR-FAQ] para:
-   Saiba quantos circuitos do ExpressRoute você pode se conectar a um gateway de ExpressRoute.
-   Saiba quantos gateways do ExpressRoute você pode se conectar a um circuito do ExpressRoute.
-   Saiba mais sobre outros limites de escala do ExpressRoute.


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


