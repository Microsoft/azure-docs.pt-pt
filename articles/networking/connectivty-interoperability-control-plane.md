---
title: 'Interoperabilidade nos recursos de conectividade de back-end do Azure: análise do plano de controle | Microsoft Docs'
description: Este artigo fornece a análise do plano de controle da configuração de teste que você pode usar para analisar a interoperabilidade entre o ExpressRoute, uma VPN site a site e um emparelhamento de rede virtual no Azure.
documentationcenter: na
services: networking
author: rambk
manager: tracsman
ms.service: virtual-network
ms.topic: article
ms.workload: infrastructure-services
ms.date: 10/18/2018
ms.author: rambala
ms.openlocfilehash: 4921e4c4fc0da95250a0171c66d6a69093b10687
ms.sourcegitcommit: 9405aad7e39efbd8fef6d0a3c8988c6bf8de94eb
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 12/05/2019
ms.locfileid: "74873850"
---
# <a name="interoperability-in-azure-back-end-connectivity-features-control-plane-analysis"></a>Interoperabilidade nos recursos de conectividade de back-end do Azure: análise do plano de controle

Este artigo descreve a análise do plano de controle da [configuração do teste][Setup]. Você também pode examinar a [configuração][Configuration] de configuração de teste e a [análise do plano de dados][Data-Analysis] da configuração do teste.

A análise de plano de controle basicamente examina as rotas trocadas entre redes em uma topologia. A análise do plano de controle pode ajudá-lo a entender como diferentes redes exibem a topologia.

## <a name="hub-and-spoke-vnet-perspective"></a>Perspectiva da VNet do Hub e do spoke

A figura a seguir ilustra a rede da perspectiva de uma rede virtual de Hub (VNet) e uma VNet spoke (realçada em azul). A figura também mostra o número de sistema autônomo (ASN) de diferentes redes e rotas que são trocadas entre redes diferentes: 

![1][1]

O ASN do gateway de ExpressRoute do Azure da VNet é diferente do ASN dos roteadores do Microsoft Enterprise Edge (MSEEs). Um gateway de ExpressRoute usa um ASN privado (um valor de **65515**) e MSEEs usa o ASN público (um valor de **12076**) globalmente. Ao configurar o emparelhamento de ExpressRoute, como o MSEE é o par, você usa **12076** como o ASN de mesmo nível. No lado do Azure, o MSEE estabelece o emparelhamento eBGP com o gateway de ExpressRoute. O emparelhamento de eBGP duplo que o MSEE estabelece para cada emparelhamento de ExpressRoute é transparente no nível do plano de controle. Portanto, ao exibir uma tabela de rota do ExpressRoute, você verá o ASN do gateway de ExpressRoute da VNet para os prefixos da VNet. 

A figura a seguir mostra uma amostra de tabela de rota do ExpressRoute: 

![5][5]

No Azure, o ASN é significativo apenas de uma perspectiva de emparelhamento. Por padrão, o ASN do gateway de ExpressRoute e do gateway de VPN no gateway de VPN do Azure é **65515**.

## <a name="on-premises-location-1-and-the-remote-vnet-perspective-via-expressroute-1"></a>Local 1 e a perspectiva da VNet remota via ExpressRoute 1

Tanto a localização local 1 quanto a VNet remota estão conectadas à VNet do hub por meio do ExpressRoute 1. Eles compartilham a mesma perspectiva da topologia, conforme mostrado no diagrama a seguir:

![2][2]

## <a name="on-premises-location-1-and-the-branch-vnet-perspective-via-a-site-to-site-vpn"></a>Local 1 e a perspectiva da VNet da filial por meio de uma VPN site a site

Tanto a localização local 1 quanto a VNet de ramificação estão conectadas a um gateway de VPN de VNet de Hub por meio de uma conexão VPN site a site. Eles compartilham a mesma perspectiva da topologia, conforme mostrado no diagrama a seguir:

![3][3]

## <a name="on-premises-location-2-perspective"></a>Perspectiva local 2

O local 2 está conectado a uma VNet de Hub por meio do emparelhamento privado do ExpressRoute 2: 

![4][4]

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

Saiba mais sobre a [análise do plano de dados][Data-Analysis] da configuração de teste e as exibições de recurso de monitoramento de rede do Azure.

Consulte as [perguntas frequentes sobre o ExpressRoute][ExR-FAQ] para:
-   Saiba quantos circuitos do ExpressRoute você pode se conectar a um gateway de ExpressRoute.
-   Saiba quantos gateways do ExpressRoute você pode se conectar a um circuito do ExpressRoute.
-   Saiba mais sobre outros limites de escala do ExpressRoute.


<!--Image References-->
[1]: ./media/backend-interoperability/HubView.png "Perspectiva do Hub e da VNet do spoke da topologia"
[2]: ./media/backend-interoperability/Loc1ExRView.png "Local 1 e perspectiva de VNet remota da topologia via ExpressRoute 1"
[3]: ./media/backend-interoperability/Loc1VPNView.png "Localização 1 e perspectiva da VNet de ramificação da topologia por meio de uma VPN site a site"
[4]: ./media/backend-interoperability/Loc2View.png "Ponto de vista local 2 da topologia"
[5]: ./media/backend-interoperability/ExR1-RouteTable.png "Tabela de rotas do ExpressRoute 1"

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


