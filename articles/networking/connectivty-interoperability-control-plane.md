---
title: 'Interoperabilidade nas funcionalidades de conectividade de back-end do Azure: Análise de plano de controlo | Documentos da Microsoft'
description: Este artigo fornece a análise de plano de controlo da configuração do teste, que pode usar para analisar a interoperabilidade entre o ExpressRoute, uma VPN site a site e da rede virtual peering no Azure.
documentationcenter: na
services: networking
author: rambk
manager: tracsman
ms.service: virtual-network
ms.topic: article
ms.workload: infrastructure-services
ms.date: 10/18/2018
ms.author: rambala
ms.openlocfilehash: 7ce50ed32f845fb21e8d95f4d2da1786e5f6f540
ms.sourcegitcommit: 5fbca3354f47d936e46582e76ff49b77a989f299
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/12/2019
ms.locfileid: "57772488"
---
# <a name="interoperability-in-azure-back-end-connectivity-features-control-plane-analysis"></a>Interoperabilidade nas funcionalidades de conectividade de back-end do Azure: Análise de plano de controlo

Este artigo descreve a análise de plano de controlo do [configuração de teste][Setup]. Também pode rever o [configuração do programa de configuração de teste] [ Configuration] e o [análise do plano de dados] [ Data-Analysis] da configuração do teste.

Análise de plano de controlo, essencialmente, examina as rotas que são trocadas entre redes dentro de uma topologia. Análise de plano de controle pode ajudar a compreender como diferentes redes ver a topologia.

## <a name="hub-and-spoke-vnet-perspective"></a>Ponto de vista de VNet do hub- and -spoke

A figura seguinte ilustra a rede da perspectiva de uma rede virtual (VNet) de hub e um spoke VNet (realçado em azul). A figura também mostra o número de sistema autónomo (ASN) de rotas que são trocadas entre várias redes e de redes diferentes: 

[![1]][1]

O ASN do gateway do ExpressRoute do Azure da VNet é diferente do ASN de Routers Enterprise Edge Microsoft (MSEEs). Um gateway do ExpressRoute utiliza um ASN privado (um valor de **65515**) e MSEEs utilizam ASN público (um valor de **12076**) globalmente. Ao configurar o peering de ExpressRoute, como MSEE é a ponto a ponto, usa **12076** como o elemento de rede ASN. No lado do Azure, MSEE estabelece eBGP peering com o gateway do ExpressRoute. O peering de eBGP dupla que o MSEE estabelece para cada peering de ExpressRoute é transparente no nível de plano de controle. Por conseguinte, quando o utilizador vê uma tabela de rota do ExpressRoute, verá o gateway do ExpressRoute da VNet ASN para prefixos da VNet. 

A figura seguinte mostra uma tabela de rota do ExpressRoute de exemplo: 

[![5]][5]

No Azure, o ASN é significativo apenas a partir de uma perspectiva de peering. Por predefinição, é o ASN do gateway do ExpressRoute e o gateway de VPN no Gateway de VPN do Azure **65515**.

## <a name="on-premises-location-1-and-the-remote-vnet-perspective-via-expressroute-1"></a>1 de localização e a perspetiva de VNet remota via 1 do ExpressRoute no local

1 de localização no local e a VNet remota estão ligados à VNet através do ExpressRoute 1 do hub. Partilham o mesmo ponto de vista da topologia, conforme mostrado no diagrama seguinte:

[![2]][2]

## <a name="on-premises-location-1-and-the-branch-vnet-perspective-via-a-site-to-site-vpn"></a>1 de localização e o ramo perspetiva de VNet através de uma VPN de site a site no local

1 de localização no local e o ramo VNet estão ligadas ao gateway de VPN da VNet hub através de uma ligação de VPN de site a site. Partilham o mesmo ponto de vista da topologia, conforme mostrado no diagrama seguinte:

[![3]][3]

## <a name="on-premises-location-2-perspective"></a>Perspetiva de 2 de localização no local

No local localização 2 está ligado a uma VNet do hub através do peering privado do ExpressRoute 2: 

[![4]][4]

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

Saiba mais sobre [análise do plano de dados] [ Data-Analysis] da configuração de teste e vistas de funcionalidade de monitorização de rede do Azure.

Consulte a [FAQ do ExpressRoute] [ ExR-FAQ] para:
-   Saiba quantos circuitos do ExpressRoute pode ligar a um gateway do ExpressRoute.
-   Saiba quantos gateways do ExpressRoute pode ligar a um circuito do ExpressRoute.
-   Saiba mais sobre outros limites de dimensionamento do ExpressRoute.


<!--Image References-->
[1]: ./media/backend-interoperability/HubView.png "hub- and -spoke VNet perspectiva da topologia"
[2]: ./media/backend-interoperability/Loc1ExRView.png "1 local e remota VNet perspetiva da topologia via 1 do ExpressRoute"
[3]: ./media/backend-interoperability/Loc1VPNView.png "1 de localização e o ramo VNet perspectiva da topologia através de uma VPN de site a site"
[4]: ./media/backend-interoperability/Loc2View.png "perspetiva de 2 de localização da topologia"
[5]: ./media/backend-interoperability/ExR1-RouteTable.png "tabela de rotas de 1 do ExpressRoute"

<!--Link References-->
[Setup]: https://docs.microsoft.com/azure/networking/connectivty-interoperability-preface
[Configuration]: https://docs.microsoft.com/azure/networking/connectivty-interoperability-config
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


