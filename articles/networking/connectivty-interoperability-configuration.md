---
title: 'Interoperabilidade nas funcionalidades de conectividade de back-end do Azure: Detalhes de configuração | Documentos da Microsoft'
description: Este artigo descreve os detalhes de configuração para a configuração de teste, que pode usar para analisar a interoperabilidade entre o ExpressRoute, uma VPN site a site e da rede virtual peering no Azure.
documentationcenter: na
services: networking
author: rambk
manager: tracsman
ms.service: virtual-network
ms.topic: article
ms.workload: infrastructure-services
ms.date: 10/18/2018
ms.author: rambala
ms.openlocfilehash: 2ceb4aeac55bd555a41c29bd41b00c771490e5f9
ms.sourcegitcommit: 5fbca3354f47d936e46582e76ff49b77a989f299
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/12/2019
ms.locfileid: "57777096"
---
# <a name="interoperability-in-azure-back-end-connectivity-features-test-configuration-details"></a>Interoperabilidade nas funcionalidades de conectividade de back-end do Azure: Detalhes de configuração de teste

Este artigo descreve os detalhes de configuração do [configuração de teste][Setup]. A configuração de teste-lo a analisar como o Azure serviços de rede interoperate ao nível de plano de controlo do e nível de plano de dados.

## <a name="spoke-vnet-connectivity-by-using-vnet-peering"></a>Conectividade de VNet do spoke ao utilizar o VNet peering

A figura seguinte mostra os detalhes do peering de rede Virtual do Azure de uma rede virtual (VNet) de spoke. Para saber como configurar o peering entre duas VNets, consulte [gerir o VNet peering][VNet-Config]. Se pretender que a VNet para utilizar os gateways que estejam ligados à VNet do hub, selecione do spoke **utilizar gateways remotos**.

[![1]][1]

A figura seguinte mostra os detalhes do peering de VNet de VNet do hub. Se pretender que o spoke VNet para utilizar os gateways de VNet do hub, selecione **utilizar gateways remotos**.

[![2]][2]

## <a name="branch-vnet-connectivity-by-using-a-site-to-site-vpn"></a>Ramificar a conectividade VNet com uma VPN de site a site

Defina a conectividade VPN de site a site entre o hub e o ramo VNets com gateways de VPN no Gateway de VPN do Azure. Por predefinição, os gateways VPN e gateways do ExpressRoute do Azure utilizam um valor de número (ASN) de sistema autónomo privada de **65515**. Pode alterar o valor ASN no Gateway de VPN. A configuração de teste, o valor ASN do gateway de VPN de VNet ramo é alterado para **65516** para suportar o encaminhamento de eBGP entre o hub e o ramo VNets.


[![3]][3]


## <a name="on-premises-location-1-connectivity-by-using-expressroute-and-a-site-to-site-vpn"></a>Conectividade de 1 de localização no local com o ExpressRoute e uma VPN de site a site

### <a name="expressroute-1-configuration-details"></a>Detalhes de configuração de 1 do ExpressRoute

A figura seguinte mostra a configuração de circuito do ExpressRoute de 1 de região do Azure no sentido de routers de periferia (CE) de cliente de 1 de localização de locais:

[![4]][4]

A figura seguinte mostra a configuração da ligação entre o circuito de ExpressRoute 1 e a VNet do hub:

[![5]][5]

A lista seguinte mostra a configuração de roteador principal do CE para conectividade de peering privada do ExpressRoute. (Os routers de ASR1000 cisco são utilizados como routers CE na configuração de teste.) Quando circuitos do ExpressRoute e VPN site a site são configurados em paralelo para ligar uma rede no local ao Azure, Azure prioriza o circuito do ExpressRoute por predefinição. Para evitar o encaminhamento assimétricas, a rede no local também deve priorizar as conectividade do ExpressRoute através de conectividade VPN de site a site. A seguinte configuração estabelece a atribuição de prioridades ao utilizar o BGP **preferência de local** atributo:

    interface TenGigabitEthernet0/0/0.300
     description Customer 30 private peering to Azure
     encapsulation dot1Q 30 second-dot1q 300
     ip vrf forwarding 30
     ip address 192.168.30.17 255.255.255.252
    !
    interface TenGigabitEthernet1/0/0.30
     description Customer 30 to south bound LAN switch
     encapsulation dot1Q 30
     ip vrf forwarding 30
     ip address 192.168.30.0 255.255.255.254
     ip ospf network point-to-point
    !
    router ospf 30 vrf 30
     router-id 10.2.30.253
     redistribute bgp 65021 subnets route-map BGP2OSPF
     network 192.168.30.0 0.0.0.1 area 0.0.0.0
    default-information originate always
     default-metric 10
    !
    router bgp 65021
     !
     address-family ipv4 vrf 30
      network 10.2.30.0 mask 255.255.255.128
      neighbor 192.168.30.18 remote-as 12076
      neighbor 192.168.30.18 activate
      neighbor 192.168.30.18 next-hop-self
      neighbor 192.168.30.18 soft-reconfiguration inbound
      neighbor 192.168.30.18 route-map prefer-ER-over-VPN in
      neighbor 192.168.30.18 prefix-list Cust30_to_Private out
     exit-address-family
    !
    route-map prefer-ER-over-VPN permit 10
     set local-preference 200
    !
    ip prefix-list Cust30_to_Private seq 10 permit 10.2.30.0/25
    !

### <a name="site-to-site-vpn-configuration-details"></a>Detalhes de configuração de VPN de site a site

A lista seguinte mostra a configuração de router CE primária para a conectividade VPN de site a site:

    crypto ikev2 proposal Cust30-azure-proposal
     encryption aes-cbc-256 aes-cbc-128 3des
     integrity sha1
     group 2
    !
    crypto ikev2 policy Cust30-azure-policy
     match address local 66.198.12.106
     proposal Cust30-azure-proposal
    !
    crypto ikev2 keyring Cust30-azure-keyring
     peer azure
      address 52.168.162.84
      pre-shared-key local IamSecure123
      pre-shared-key remote IamSecure123
    !
    crypto ikev2 profile Cust30-azure-profile
     match identity remote address 52.168.162.84 255.255.255.255
     identity local address 66.198.12.106
     authentication local pre-share
     authentication remote pre-share
     keyring local Cust30-azure-keyring
    !
    crypto ipsec transform-set Cust30-azure-ipsec-proposal-set esp-aes 256 esp-sha-hmac
     mode tunnel
    !
    crypto ipsec profile Cust30-azure-ipsec-profile
     set transform-set Cust30-azure-ipsec-proposal-set
     set ikev2-profile Cust30-azure-profile
    !
    interface Loopback30
     ip address 66.198.12.106 255.255.255.255
    !
    interface Tunnel30
     ip vrf forwarding 30
     ip address 10.2.30.125 255.255.255.255
     tunnel source Loopback30
     tunnel mode ipsec ipv4
     tunnel destination 52.168.162.84
     tunnel protection ipsec profile Cust30-azure-ipsec-profile
    !
    router bgp 65021
     !
     address-family ipv4 vrf 30
      network 10.2.30.0 mask 255.255.255.128
      neighbor 10.10.30.254 remote-as 65515
      neighbor 10.10.30.254 ebgp-multihop 5
      neighbor 10.10.30.254 update-source Tunnel30
      neighbor 10.10.30.254 activate
      neighbor 10.10.30.254 soft-reconfiguration inbound
     exit-address-family
    !
    ip route vrf 30 10.10.30.254 255.255.255.255 Tunnel30

## <a name="on-premises-location-2-connectivity-by-using-expressroute"></a>Conectividade de 2 de localização no local com o ExpressRoute

Um circuito do ExpressRoute segunda, na proximidade mais de perto a 2 de localização no local, liga-se 2 de localização no local à VNet do hub. A figura seguinte mostra a segunda configuração de ExpressRoute:

[![6]][6]

A figura seguinte mostra a configuração da ligação entre o segundo circuito do ExpressRoute e a VNet do hub:

[![7]][7]

1 do ExpressRoute liga-se a VNet do hub e a 1 de localização no local a uma VNet remota numa região diferente do Azure:

[![8]][8]

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

Saiba mais sobre [controlar a análise de plano] [ Control-Analysis] da configuração de teste e as vistas de VNets de diferentes ou VLANs na topologia.

Saiba mais sobre [análise do plano de dados] [ Data-Analysis] da configuração de teste e vistas de funcionalidade de monitorização de rede do Azure.

Consulte a [FAQ do ExpressRoute] [ ExR-FAQ] para:
-   Saiba quantos circuitos do ExpressRoute pode ligar a um gateway do ExpressRoute.
-   Saiba quantos gateways do ExpressRoute pode ligar a um circuito do ExpressRoute.
-   Saiba mais sobre outros limites de dimensionamento do ExpressRoute.


<!--Image References-->
[1]: ./media/backend-interoperability/SpokeVNet_peering.png "falava VNet peering de VNet"
[2]: ./media/backend-interoperability/HubVNet-peering.png "da VNet do hub o VNet peering"
[3]: ./media/backend-interoperability/BranchVNet-VPNGW.png "configuração do Gateway de VPN de um ramo de VNet"
[4]: ./media/backend-interoperability/ExR1.png "configuração 1 do ExpressRoute"
[5]: ./media/backend-interoperability/ExR1-Hub-Connection.png "configuração da ligação de 1 do ExpressRoute para um hub de gateway de VNet ExR"
[6]: ./media/backend-interoperability/ExR2.png "configuração 2 do ExpressRoute"
[7]: ./media/backend-interoperability/ExR2-Hub-Connection.png "configuração da ligação de ExpressRoute 2 para um hub de gateway de VNet ExR"
[8]: ./media/backend-interoperability/ExR2-Remote-Connection.png "configuração da ligação de ExpressRoute 2 para um gateway de VNet ExR remoto"

<!--Link References-->
[Setup]: https://docs.microsoft.com/azure/networking/connectivty-interoperability-preface
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


