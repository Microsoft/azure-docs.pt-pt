---
title: 'Interoperabilidade em funcionalidades de conectividade traseira Azure: Detalhes de configuração | Microsoft Docs'
description: Este artigo descreve detalhes de configuração para a configuração do teste que pode usar para analisar a interoperabilidade entre o ExpressRoute, uma VPN site-to-site e o olhar de rede virtual em Azure.
documentationcenter: na
services: networking
author: rambk
manager: tracsman
ms.service: virtual-network
ms.topic: article
ms.workload: infrastructure-services
ms.date: 10/18/2018
ms.author: rambala
ms.openlocfilehash: 50fd5641c61d08939eca1f2cbafb1d077254e37d
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/19/2021
ms.locfileid: "98233275"
---
# <a name="interoperability-in-azure-back-end-connectivity-features-test-configuration-details"></a>Interoperabilidade em funcionalidades de conectividade traseira do Azure: Detalhes da configuração do teste

Este artigo descreve os detalhes de configuração da [configuração][Setup]do teste . A configuração do teste ajuda-o a analisar como os serviços de networking Azure interoperam ao nível do plano de controlo e ao nível do plano de dados.

## <a name="spoke-vnet-connectivity-by-using-vnet-peering"></a>Conectividade falada VNet usando o peering VNet

A seguinte figura mostra a Rede Virtual Azure a espreitar detalhes de uma rede virtual falada (VNet). Para aprender a configurar olhando entre dois VNets, consulte [Gerir o olhar da VNet][VNet-Config]. Se pretender que o VNet falado utilize os gateways que estão ligados ao hub VNet, selecione **Utilize gateways remotos**.

[![1]][1]

A figura a seguir mostra os detalhes de espreitar vNet do hub VNet. Se pretender que o hub VNet permita que o VNet falado utilize os gateways do hub, selecione Permitir o **trânsito de gateway**.

[![2]][2]

## <a name="branch-vnet-connectivity-by-using-a-site-to-site-vpn"></a>Conectividade branch VNet utilizando uma VPN site-to-site

Confifique a conectividade VPN local entre o hub e os VNets de ramo utilizando gateways VPN em Azure VPN Gateway. Por predefinição, os gateways VPN e os gateways Azure ExpressRoute utilizam um valor de sistema autónomo privado (ASN) de **65515**. Pode alterar o valor ASN no Gateway VPN. Na configuração do teste, o valor ASN do gateway VNet VPN da filial é alterado para **65516** para suportar o encaminhamento eBGP entre o hub e os VNets de ramo.


[![3]][3]


## <a name="on-premises-location-1-connectivity-by-using-expressroute-and-a-site-to-site-vpn"></a>Conectividade no local localização 1 utilizando ExpressRoute e uma VPN site-to-site

### <a name="expressroute-1-configuration-details"></a>ExpressRoute 1 detalhes de configuração

A seguinte figura mostra a configuração do circuito Azure Region 1 ExpressRoute em direção aos routers de borda do cliente (CE) no local:

[![4]][4]

A seguinte figura mostra a configuração de ligação entre o circuito ExpressRoute 1 e o hub VNet:

[![5]][5]

A lista a seguir mostra a configuração do router CE primário para a conectividade de espreitamento privado ExpressRoute. (Os routers Cisco ASR1000 são utilizados como routers CE na configuração do teste.) Quando os circuitos local-a-local VPN e ExpressRoute são configurados em paralelo para ligar uma rede no local ao Azure, o Azure prioriza o circuito ExpressRoute por defeito. Para evitar o encaminhamento assimétrico, a rede no local também deve priorizar a conectividade ExpressRoute sobre a conectividade VPN local-local. A seguinte configuração estabelece a priorização utilizando o atributo **de preferência local** BGP:

```config
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
```

### <a name="site-to-site-vpn-configuration-details"></a>Detalhes de configuração VPN site-to-site

A lista a seguir mostra a configuração do router CE primário para a conectividade VPN local-a-local:

```config
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
```

## <a name="on-premises-location-2-connectivity-by-using-expressroute"></a>Conectividade no local localização 2 utilizando ExpressRoute

Um segundo circuito ExpressRoute, na proximidade mais próxima do local 2, liga o local 2 ao hub VNet. A seguinte figura mostra a segunda configuração ExpressRoute:

[![6]][6]

A seguinte figura mostra a configuração de ligação entre o segundo circuito ExpressRoute e o hub VNet:

[![7]][7]

O ExpressRoute 1 liga tanto o hub VNet como o local 1 a um VNet remoto numa região de Azure diferente:

[![8]][8]

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

Saiba mais sobre a análise do plano de [controlo][Control-Analysis] da configuração do teste e as vistas de diferentes VNets ou VLANs na topologia.

Saiba mais sobre a análise do plano de [dados][Data-Analysis] da configuração do teste e as vistas do recurso de monitorização da rede Azure.

Consulte as [FAQ expressRoute][ExR-FAQ] para:
-   Saiba quantos circuitos ExpressRoute pode ligar a um gateway ExpressRoute.
-   Saiba quantos gateways ExpressRoute pode ligar a um circuito ExpressRoute.
-   Saiba mais sobre outros limites de escala do ExpressRoute.


<!--Image References-->
[1]: ./media/backend-interoperability/SpokeVNet_peering.png "Falou VNet's peering"
[2]: ./media/backend-interoperability/HubVNet-peering.png "Hub VNet's VNet olhando"
[3]: ./media/backend-interoperability/BranchVNet-VPNGW.png "Configuração VPN Gateway de um ramo VNet"
[4]: ./media/backend-interoperability/ExR1.png "Configuração ExpressRoute 1"
[5]: ./media/backend-interoperability/ExR1-Hub-Connection.png "Configuração de ligação do ExpressRoute 1 a um gateway VNet ExR hub"
[6]: ./media/backend-interoperability/ExR2.png "Configuração ExpressRoute 2"
[7]: ./media/backend-interoperability/ExR2-Hub-Connection.png "Configuração de ligação do ExpressRoute 2 a um gateway VNet ExR hub"
[8]: ./media/backend-interoperability/ExR2-Remote-Connection.png "Configuração de ligação do ExpressRoute 2 a um gateway remoto VNet ExR"

<!--Link References-->
[Setup]: ./connectivty-interoperability-preface.md
[ExpressRoute]: ../expressroute/expressroute-introduction.md
[VPN]: ../vpn-gateway/vpn-gateway-about-vpngateways.md
[VNet]: ../virtual-network/tutorial-connect-virtual-networks-portal.md
[Control-Analysis]: ./connectivty-interoperability-control-plane.md
[Data-Analysis]: ./connectivty-interoperability-data-plane.md
[ExR-FAQ]: ../expressroute/expressroute-faqs.md
[S2S-Over-ExR]: ../expressroute/site-to-site-vpn-over-microsoft-peering.md
[ExR-S2S-CoEx]: ../expressroute/expressroute-howto-coexist-resource-manager.md
[Hub-n-Spoke]: /azure/architecture/reference-architectures/hybrid-networking/hub-spoke
[Deploy-NVA]: /azure/architecture/reference-architectures/dmz/nva-ha
[VNet-Config]: ../virtual-network/virtual-network-manage-peering.md