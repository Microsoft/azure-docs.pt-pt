---
title: 'Interoperabilidade nas funcionalidades de conectividade back-end do Azure: Detalhes de configuração / Microsoft Docs'
description: Este artigo descreve detalhes de configuração para a configuração do teste que pode usar para analisar a interoperabilidade entre o ExpressRoute, uma VPN site-to-site e o peering de rede virtual em Azure.
documentationcenter: na
services: networking
author: rambk
manager: tracsman
ms.service: virtual-network
ms.topic: article
ms.workload: infrastructure-services
ms.date: 10/18/2018
ms.author: rambala
ms.openlocfilehash: 9c4a57111566248d3537cab0d9d85c0c3be874a1
ms.sourcegitcommit: 6a4fbc5ccf7cca9486fe881c069c321017628f20
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/27/2020
ms.locfileid: "68335937"
---
# <a name="interoperability-in-azure-back-end-connectivity-features-test-configuration-details"></a>Interoperabilidade nas funcionalidades de conectividade traseira do Azure: Detalhes de configuração de teste

Este artigo descreve os detalhes de configuração da [configuração][Setup]do teste . A configuração do teste ajuda-o a analisar como os serviços de rede Azure interoperam ao nível do plano de controlo e do plano de dados.

## <a name="spoke-vnet-connectivity-by-using-vnet-peering"></a>Conectividade VNet falada usando o peering VNet

A figura que se segue mostra a Rede Virtual Azure a analisar detalhes de uma rede virtual falada (VNet). Para aprender a configurar o peering entre dois VNets, consulte [Manage VNet peering][VNet-Config]. Se desejar que o VNet falado utilize os gateways ligados ao hub VNet, selecione **Utilize gateways remotos**.

[![1]][1]

A figura que se segue mostra o VNet a espreitar detalhes do hub VNet. Se pretender que o hub VNet permita que o VNet falado utilize os gateways do hub, **selecione Permitir**o trânsito de gateway .

[![2]][2]

## <a name="branch-vnet-connectivity-by-using-a-site-to-site-vpn"></a>Conectividade Branch VNet utilizando uma VPN site-to-site

Criar conectividade VPN site-to-site entre o hub e o ramo VNets utilizando gateways VPN em Gateway VPN Azure. Por predefinição, os gateways VPN e os gateways Azure ExpressRoute utilizam um valor de número de sistema autónomo privado (ASN) de **65515**. Pode alterar o valor ASN em VPN Gateway. Na configuração do teste, o valor ASN do porta-gateway VNet VPN da sucursal é alterado para **65516** para suportar o encaminhamento eBGP entre o hub e o ramo VNets.


[![3]][3]


## <a name="on-premises-location-1-connectivity-by-using-expressroute-and-a-site-to-site-vpn"></a>No local Localização 1 conectividade utilizando expressRoute e uma VPN site-to-site

### <a name="expressroute-1-configuration-details"></a>Detalhes de configuração ExpressRoute 1

A figura que se segue mostra a configuração do circuito Azure Region 1 ExpressRoute para os routers de borda do cliente (CE) no local:

[![4]][4]

A figura seguinte mostra a configuração de ligação entre o circuito ExpressRoute 1 e o hub VNet:

[![5]][5]

A lista que se segue mostra a configuração primária do router CE para a conectividade de peering privado ExpressRoute. (Os routers Cisco ASR1000 são utilizados como routers CE na configuração do teste.) Quando os circuitos VPN e ExpressRoute do local estão configurados paralelamente para ligar uma rede no local ao Azure, o Azure prioriza o circuito ExpressRoute por padrão. Para evitar o encaminhamento assimétrico, a rede no local também deve priorizar a conectividade ExpressRoute em relação à conectividade VPN site-to-site. A seguinte configuração estabelece a priorização utilizando o atributo **de preferência local** bGP:

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

### <a name="site-to-site-vpn-configuration-details"></a>Detalhes de configuração VPN site-to-site

A lista que se segue mostra a configuração primária do router CE para a conectividade VPN site-to-site:

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

## <a name="on-premises-location-2-connectivity-by-using-expressroute"></a>No local Localização 2 conectividade utilizando expressRoute

Um segundo circuito ExpressRoute, mais próximo do local local 2, liga o local 2 às instalações do centro VNet. A figura que se segue mostra a segunda configuração ExpressRoute:

[![6]][6]

A figura seguinte mostra a configuração de ligação entre o segundo circuito ExpressRoute e o hub VNet:

[![7]][7]

ExpressRoute 1 liga tanto o hub VNet como o local no local 1 a um VNet remoto numa região azure diferente:

[![8]][8]

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

Conheça a análise do plano de [controlo][Control-Analysis] da configuração do teste e as vistas de diferentes VNets ou VLANs na topologia.

Conheça a análise de [planos][Data-Analysis] de dados da configuração do teste e das vistas da funcionalidade de monitorização da rede Azure.

Consulte o [ExpressRoute FAQ][ExR-FAQ] para:
-   Saiba quantos circuitos ExpressRoute pode ligar a um gateway ExpressRoute.
-   Saiba quantos gateways ExpressRoute pode ligar a um circuito ExpressRoute.
-   Conheça outros limites de escala da ExpressRoute.


<!--Image References-->
[1]: ./media/backend-interoperability/SpokeVNet_peering.png "VNet de vnet falado peering"
[2]: ./media/backend-interoperability/HubVNet-peering.png "Hub VNet's VNet peering"
[Configuração de Gateway 3]: ./media/backend-interoperability/BranchVNet-VPNGW.png "VPN de um ramo VNet"
[4]: ./media/backend-interoperability/ExR1.png "Configuração ExpressRoute 1"
[5]: ./media/backend-interoperability/ExR1-Hub-Connection.png "Configuração de ligação do ExpressRoute 1 a um portal VNet ExR hub"
[6]: ./media/backend-interoperability/ExR2.png "Configuração ExpressRoute 2"
[7]: ./media/backend-interoperability/ExR2-Hub-Connection.png "Configuração de ligação do ExpressRoute 2 a um portal VNet ExR hub"
[8]: ./media/backend-interoperability/ExR2-Remote-Connection.png "Configuração de ligação do ExpressRoute 2 a um gateway VNet ExR remoto"

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


