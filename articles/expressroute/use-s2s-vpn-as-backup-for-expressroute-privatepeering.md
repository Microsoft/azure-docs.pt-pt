---
title: Utilização do S2S VPN como cópia de segurança para o Azure ExpressRoute Private Peering [ Microsoft Docs
description: Esta página fornece recomendações arquitetónicas para apoiar o peering privado Azure ExpressRoute com a S2S VPN.
services: networking
author: rambk
ms.service: expressroute
ms.topic: article
ms.date: 02/05/2020
ms.author: rambala
ms.openlocfilehash: a6a22b667bc66d6ee69bfbd7ad1db88f72d8df0e
ms.sourcegitcommit: acb82fc770128234f2e9222939826e3ade3a2a28
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/21/2020
ms.locfileid: "81687833"
---
# <a name="using-s2s-vpn-as-a-backup-for-expressroute-private-peering"></a>Utilização de VpN S2S como cópia de segurança para o peering privado ExpressRoute

No artigo intitulado Design para a recuperação de [desastres com o peering privado ExpressRoute,][DR-PP]discutimos a necessidade de uma solução de conectividade de backup para uma conectividade privada ExpressRoute e como usar circuitos geo-redundantes expressRoute para o efeito. Neste artigo, consideremos como alavancar e manter o VPN site-to-site (S2S) como um back para o peering privado ExpressRoute. 

Ao contrário dos circuitos geo-redundantes da ExpressRoute, só pode utilizar a combinação de recuperação de desastres ExpressRoute-VPN em modo passivo ativo. Um grande desafio de usar qualquer conectividade de rede de backup no modo passivo é que a ligação passiva muitas vezes falharia ao lado da ligação primária. A razão comum para as falhas da ligação passiva é a falta de manutenção ativa. Portanto, neste artigo vamos focar-nos em como verificar e manter ativamente a conectividade VPN S2S que está a apoiar um peering privado ExpressRoute.

>[!NOTE] 
>Quando uma determinada rota é publicitada através da ExpressRoute e VPN, o Azure preferiria o encaminhamento sobre a ExpressRoute.  
>

Neste artigo, vamos ver como verificar a conectividade tanto do ponto de vista do Azure como da perspetiva do lado da rede do cliente. A capacidade de validar de qualquer uma das extremidades ajudará independentemente de gerir ou não os dispositivos de rede do lado do cliente que se peer com as entidades da rede Microsoft. 

## <a name="example-topology"></a>Exemplo de topologia

Na nossa configuração, temos uma rede no local ligada a um Hub Azure VNet através de um circuito ExpressRoute e uma ligação VPN S2S. O Hub Azure VNet é, por sua vez, espreitado por um VNet falado, como mostra o diagrama abaixo:

![1][1]

Na configuração, o circuito ExpressRoute é encerrado num par de routers "Customer Edge" (CE) no local. O LAN no local está ligado aos routers CE através de um par de firewalls que operam em modo de seguidor de líder. A VPN S2S é diretamente terminada nas firewalls.

A tabela seguinte enumera os prefixos IP chave da topologia:

| **Entidade** | **Prefixo** |
| --- | --- |
| No local LAN | 10.1.11.0/25 |
| Azure Hub VNet | 10.17.11.0/25 |
| Azure falou VNet | 10.17.11.128/26 |
| Servidor de teste no local | 10.1.11.10 |
| VM de teste VNet falado | 10.17.11.132 |
| Sub-rede de ligação primária ExpressRoute p2p | 192.168.11.16/30 |
| Sub-rede de ligação secundária ExpressRoute p2p | 192.168.11.20/30 |
| VPN gateway primária BGP peer IP | 10.17.11.76 |
| VPN gateway secundário BGP peer IP | 10.17.11.77 |
| Firewall no local VPN BGP peer IP | 192.168.11.88 |
| Router CE primário i/f em direção ao IP de firewall | 192.168.11.0/31 |
| Firewall i/f rumo ao router primário CE router IP | 192.168.11.1/31 |
| Router CE secundário i/f em direção ao IP da firewall | 192.168.11.2/31 |
| Firewall i/f rumo ao router SECUNDÁRIO CE router IP | 192.168.11.3/31 |


A tabela seguinte lista as ASNs da topologia:

| **Sistema autónomo** | **ASN** |
| --- | --- |
| Local | 65020 |
| Microsoft Enterprise Edge | 12076 |
| Rede Virtual GW (Exr) | 65515 |
| Rede Virtual GW (VPN) | 65515 |

## <a name="high-availability-without-asymmetricity"></a>Alta disponibilidade sem assimétrica

### <a name="configuring-for-high-availability"></a>Configuração para alta disponibilidade

[Configurar as ligações coexistentes ExpressRoute e Site-to-Site][Conf-CoExist] discute como configurar o circuito ExpressRoute coexistente e as ligações VPN S2S. Como discutimos em Design para uma elevada disponibilidade com o [ExpressRoute,][HA]para melhorar a elevada disponibilidade da ExpressRoute, a nossa configuração mantém o despedimento da rede (evita um único ponto de falha) até aos pontos finais. Também as ligações primárias e secundárias dos circuitos ExpressRoute estão configuradas para funcionar em modo activo- ativo, publicitando os prefixos no local da mesma forma através de ambas as ligações. 

O anúncio de rota no local do router CE primário através da ligação primária do circuito ExpressRoute é mostrado abaixo (comandos Junos):

    user@SEA-MX03-01> show route advertising-protocol bgp 192.168.11.18 

    Cust11.inet.0: 8 destinations, 8 routes (7 active, 0 holddown, 1 hidden)
      Prefix                  Nexthop              MED     Lclpref    AS path
    * 10.1.11.0/25            Self                                    I

O anúncio de rota no local do router CE secundário através da ligação secundária do circuito ExpressRoute é mostrado abaixo (comandos Junos):

    user@SEA-MX03-02> show route advertising-protocol bgp 192.168.11.22 

    Cust11.inet.0: 8 destinations, 8 routes (7 active, 0 holddown, 1 hidden)
      Prefix                  Nexthop              MED     Lclpref    AS path
    * 10.1.11.0/25            Self                                    I

Para melhorar a elevada disponibilidade da ligação de backup, o S2S VPN também está configurado no modo ativo. A configuração de gateway Azure VPN é mostrada abaixo. Nota como parte da configuração VPN VPN os endereços IP peer BGP do gateway --10.17.11.76 e 10.17.11.77-- também estão listados.

![2][2]

A rota no local é anunciada pelas firewalls aos pares bGP primários e secundários da porta de entrada VPN. Os anúncios de rotas são mostrados abaixo (Junos):

    user@SEA-SRX42-01> show route advertising-protocol bgp 10.17.11.76 

    Cust11.inet.0: 14 destinations, 21 routes (14 active, 0 holddown, 0 hidden)
      Prefix                  Nexthop              MED     Lclpref    AS path
    * 10.1.11.0/25            Self                                    I

    {primary:node0}
    user@SEA-SRX42-01> show route advertising-protocol bgp 10.17.11.77    

    Cust11.inet.0: 14 destinations, 21 routes (14 active, 0 holddown, 0 hidden)
      Prefix                  Nexthop              MED     Lclpref    AS path
    * 10.1.11.0/25            Self                                    I

>[!NOTE] 
>Configurar o S2S VPN em modo ativo não só fornece alta disponibilidade para a conectividade da rede de backup de recuperação de desastres, como também fornece uma maior entrada para a conectividade de backup. Por outras palavras, a configuração do S2S VPN em modo ativo ativo é recomendada, uma vez que força a criar vários túneis subjacentes.
>

### <a name="configuring-for-symmetric-traffic-flow"></a>Configuração para fluxo de tráfego simétrico

Notamos que quando uma determinada rota no local é publicitada através da ExpressRoute e da S2S VPN, o Azure preferiria o caminho ExpressRoute. Para forçar o Azure a preferir o caminho S2S VPN sobre a Rota Expresso coexistente, é necessário anunciar rotas mais específicas (prefixo mais longo com máscara de sub-rede maior) através da ligação VPN. O nosso objetivo aqui é usar as ligações VPN apenas de volta. Então, o comportamento de seleção de caminhos padrão do Azure está em linha com o nosso objetivo. 

É nossa responsabilidade garantir que o tráfego destinado a Azure a partir do local também prefere o caminho ExpressRoute em vez do S2S VPN. A preferência local padrão dos routers CE e firewalls na nossa configuração no local é de 100. Assim, configurando a preferência local das rotas recebidas através dos pares privados ExpressRoute superiores a 100 (digamos 150), podemos fazer o tráfego destinado ao Circuito ExpressRoute preferir o circuito ExpressRoute em estado estável.

A configuração BGP do router CE primário que termina a ligação primária do circuito ExpressRoute é mostrada abaixo. Note que o valor da preferência local das rotas anunciadas durante a sessão do IBGP está configurado para ser de 150. Da mesma forma, precisamos garantir que a preferência local do router CE secundário que termina a ligação secundária do circuito ExpressRoute também é configurada como 150.

    user@SEA-MX03-01> show configuration routing-instances Cust11 
    description "Customer 11 VRF";
    instance-type virtual-router;
    interface xe-0/0/0:0.110;
    interface ae0.11;
    protocols {
      bgp {
        group ibgp {
            type internal;
            local-preference 150;
            neighbor 192.168.11.1;
        }
        group ebgp {
            peer-as 12076;
            bfd-liveness-detection {
                minimum-interval 300;
                multiplier 3;
            }
            neighbor 192.168.11.18;
        }
      }
    }

A tabela de encaminhamento das firewalls no local confirma (mostrado abaixo) que para o tráfego no local que se destina a Azure o caminho preferido é sobre a ExpressRoute em estado constante.

    user@SEA-SRX42-01> show route table Cust11.inet.0 10.17.11.0/24    

    Cust11.inet.0: 14 destinations, 21 routes (14 active, 0 holddown, 0 hidden)
    + = Active Route, - = Last Active, * = Both

    10.17.11.0/25      *[BGP/170] 2d 00:34:04, localpref 150
                          AS path: 12076 I, validation-state: unverified
                        > to 192.168.11.0 via reth1.11
                          to 192.168.11.2 via reth2.11
                        [BGP/170] 2d 00:34:01, localpref 150
                          AS path: 12076 I, validation-state: unverified
                        > to 192.168.11.2 via reth2.11
                        [BGP/170] 2d 21:12:13, localpref 100, from 10.17.11.76
                          AS path: 65515 I, validation-state: unverified
                        > via st0.118
                        [BGP/170] 2d 00:41:51, localpref 100, from 10.17.11.77
                          AS path: 65515 I, validation-state: unverified
                        > via st0.119
    10.17.11.76/32     *[Static/5] 2d 21:12:16
                        > via st0.118
    10.17.11.77/32     *[Static/5] 2d 00:41:56
                        > via st0.119
    10.17.11.128/26    *[BGP/170] 2d 00:34:04, localpref 150
                          AS path: 12076 I, validation-state: unverified
                        > to 192.168.11.0 via reth1.11
                          to 192.168.11.2 via reth2.11
                        [BGP/170] 2d 00:34:01, localpref 150
                          AS path: 12076 I, validation-state: unverified
                        > to 192.168.11.2 via reth2.11
                        [BGP/170] 2d 21:12:13, localpref 100, from 10.17.11.76
                          AS path: 65515 I, validation-state: unverified
                        > via st0.118
                        [BGP/170] 2d 00:41:51, localpref 100, from 10.17.11.77
                          AS path: 65515 I, validation-state: unverified
                        > via st0.119

Na tabela de rotas acima, para o hub e as rotas vNet faladas -- 10.17.11.0/25 e 10.17.11.128/26 -- vemos que o circuito ExpressRoute é preferido em relação às ligações VPN. Os 192.168.11.0 e 192.168.11.2 são IPs na interface de firewall em direção aos routers CE.

## <a name="validation-of-route-exchange-over-s2s-vpn"></a>Validação da troca de rotas através do S2S VPN

No início deste artigo, verificámos no local a publicidade de rota das firewalls para os pares primários e secundários do BGP da porta de entrada VPN. Além disso, vamos confirmar as rotas Azure recebidas pelas firewalls dos pares bGP primários e secundários da porta de entrada VPN.

    user@SEA-SRX42-01> show route receive-protocol bgp 10.17.11.76 table Cust11.inet.0 

    Cust11.inet.0: 14 destinations, 21 routes (14 active, 0 holddown, 0 hidden)
      Prefix                  Nexthop              MED     Lclpref    AS path
      10.17.11.0/25           10.17.11.76                             65515 I
      10.17.11.128/26         10.17.11.76                             65515 I

    {primary:node0}
    user@SEA-SRX42-01> show route receive-protocol bgp 10.17.11.77 table Cust11.inet.0    

    Cust11.inet.0: 14 destinations, 21 routes (14 active, 0 holddown, 0 hidden)
      Prefix                  Nexthop              MED     Lclpref    AS path
      10.17.11.0/25           10.17.11.77                             65515 I
      10.17.11.128/26         10.17.11.77                             65515 I

Da mesma forma, vamos verificar se estão as prefixações da rota da rede no local recebidas pelo gateway Azure VPN. 

    PS C:\Users\user> Get-AzVirtualNetworkGatewayLearnedRoute -ResourceGroupName SEA-Cust11 -VirtualNetworkGatewayName SEA-Cust11-VNet01-gw-vpn | where {$_.Network -eq "10.1.11.0/25"} | select Network, NextHop, AsPath, Weight

    Network      NextHop       AsPath      Weight
    -------      -------       ------      ------
    10.1.11.0/25 192.168.11.88 65020        32768
    10.1.11.0/25 10.17.11.76   65020        32768
    10.1.11.0/25 10.17.11.69   12076-65020  32769
    10.1.11.0/25 10.17.11.69   12076-65020  32769
    10.1.11.0/25 192.168.11.88 65020        32768
    10.1.11.0/25 10.17.11.77   65020        32768
    10.1.11.0/25 10.17.11.69   12076-65020  32769
    10.1.11.0/25 10.17.11.69   12076-65020  32769

Como visto acima, o gateway VPN tem rotas recebidas tanto pelos pares primários como secundários do BGP gateway. Tem também visibilidade sobre as rotas recebidas através de ligações primárias e secundárias do ExpressRoute (as que têm percurso as-caminho preparado com 12076). Para confirmar as rotas recebidas através de ligações VPN, precisamos de conhecer o IP de pares bGP no local das ligações. Na nossa configuração em análise, é 192.168.11.88 e vemos as rotas recebidas dela.

Em seguida, vamos verificar as rotas anunciadas pela porta de entrada Azure VPN para o firewall bGP peer no local (192.168.11.88).

    PS C:\Users\user> Get-AzVirtualNetworkGatewayAdvertisedRoute -Peer 192.168.11.88 -ResourceGroupName SEA-Cust11 -VirtualNetworkGatewayName SEA-Cust11-VNet01-gw-vpn |  select Network, NextHop, AsPath, Weight

    Network         NextHop     AsPath Weight
    -------         -------     ------ ------
    10.17.11.0/25   10.17.11.76 65515       0
    10.17.11.128/26 10.17.11.76 65515       0
    10.17.11.0/25   10.17.11.77 65515       0
    10.17.11.128/26 10.17.11.77 65515       0


A não ver as trocas de rotas indica falha de ligação. Ver [Resolução de Problemas: Uma ligação VPN de site-para-local azure não pode ligar e para][VPN Troubleshoot] de trabalhar para ajudar na resolução de problemas da ligação VPN.

## <a name="testing-failover"></a>Falha de teste

Agora que confirmamos trocas de rotas bem sucedidas sobre a ligação VPN (plano de controlo), estamos prontos para mudar o tráfego (data plane) da conectividade ExpressRoute para a conectividade VPN. 

>[!NOTE] 
>Nos ambientes de produção, os testes de failover têm de ser feitos durante a janela de trabalho de manutenção de rede programada, uma vez que pode ser disruptivo pelo serviço.
>

Antes de fazer o interruptor de tráfego, vamos rastrear o caminho atual na nossa configuração desde o servidor de teste no local até ao VM de teste no VNet falado.

    C:\Users\PathLabUser>tracert 10.17.11.132

    Tracing route to 10.17.11.132 over a maximum of 30 hops

      1    <1 ms    <1 ms    <1 ms  10.1.11.1
      2    <1 ms    <1 ms    11 ms  192.168.11.0
      3    <1 ms    <1 ms    <1 ms  192.168.11.18
      4     *        *        *     Request timed out.
      5     6 ms     6 ms     5 ms  10.17.11.132

    Trace complete.

As subredes de ligação ponto a ponto da via expressa primária e secundária da nossa configuração são, respectivamente, 192.168.11.16.16/30 e 192.168.11.20/30. Na rota de rastreio acima, no passo 3 vemos que estamos a atingir 192.168.11.18, que é a interface IP do MSEE primário. A presença da interface MSEE confirma que, como esperado, o nosso percurso atual é sobre a Rota Expresso.

Como relatado nos pares do [circuito Reset ExpressRoute,][RST]vamos usar os seguintes comandos powershell para desativar tanto o epeering primário como secundário do circuito ExpressRoute.

    $ckt = Get-AzExpressRouteCircuit -Name "expressroute name" -ResourceGroupName "SEA-Cust11"
    $ckt.Peerings[0].State = "Disabled"
    Set-AzExpressRouteCircuit -ExpressRouteCircuit $ckt

O tempo de transição depende do tempo de convergência do BGP. Na nossa configuração, o interruptor failover demora alguns segundos (menos de 10). Após o interruptor, a repetição da rota de rastreio mostra o seguinte caminho:

    C:\Users\PathLabUser>tracert 10.17.11.132

    Tracing route to 10.17.11.132 over a maximum of 30 hops

      1    <1 ms    <1 ms    <1 ms  10.1.11.1
      2     *        *        *     Request timed out.
      3     6 ms     7 ms     9 ms  10.17.11.132

    Trace complete.

O resultado da rota de rastreio confirma que a ligação de backup via S2S VPN está ativa e pode fornecer continuidade de serviço se as ligações primárias e secundárias da ExpressRoute falharem. Para completar os testes de failover, vamos ativar as ligações ExpressRoute de volta e normalizar o fluxo de tráfego, utilizando o seguinte conjunto de comandos.

    $ckt = Get-AzExpressRouteCircuit -Name "expressroute name" -ResourceGroupName "SEA-Cust11"
    $ckt.Peerings[0].State = "Enabled"
    Set-AzExpressRouteCircuit -ExpressRouteCircuit $ckt

Para confirmar que o tráfego é reencaminhado para a ExpressRoute, repita a rota de rastreio e certifique-se de que está a passar pelo desvio privado expressRoute.

## <a name="next-steps"></a>Passos seguintes

O ExpressRoute foi concebido para uma elevada disponibilidade sem um único ponto de falha dentro da rede Microsoft. Ainda assim, um circuito ExpressRoute está confinado a uma única região geográfica e a um prestador de serviços. S2S VPN pode ser uma boa solução passiva de recuperação de desastres para um circuito ExpressRoute. Para uma solução de ligação passiva de reserva fiável, a manutenção regular da configuração passiva e validação periódica a ligação é importante. É essencial não deixar a configuração VPN ficar velha, e repetir periodicamente (digamos cada trimestre) as etapas de validação e failover descritas neste artigo durante a janela de manutenção.

Para ativar a monitorização e alertas com base nas métricas de gateway VPN, consulte [Os alertas de configuração nas métricas VPN Gateway][VPN-alerts].

Para acelerar a convergência do BGP na sequência de uma falha expressRoute, [configure BFD sobre ExpressRoute][BFD].

<!--Image References-->
[1]: ./media/use-s2s-vpn-as-backup-for-expressroute-privatepeering/topology.png "topologia em consideração"
[2]: ./media/use-s2s-vpn-as-backup-for-expressroute-privatepeering/vpn-gw-config.png "Configuração VPN GW"

<!--Link References-->
[DR-PP]: https://docs.microsoft.com/azure/expressroute/designing-for-disaster-recovery-with-expressroute-privatepeering
[Conf-CoExist]: https://docs.microsoft.com/azure/expressroute/expressroute-howto-coexist-resource-manager
[HA]: https://docs.microsoft.com/azure/expressroute/designing-for-high-availability-with-expressroute
[VPN Troubleshoot]: https://docs.microsoft.com/azure/vpn-gateway/vpn-gateway-troubleshoot-site-to-site-cannot-connect
[VPN-alerts]: https://docs.microsoft.com/azure/vpn-gateway/vpn-gateway-howto-setup-alerts-virtual-network-gateway-metric
[BFD]: https://docs.microsoft.com/azure/expressroute/expressroute-bfd
[RST]: https://docs.microsoft.com/azure/expressroute/expressroute-howto-reset-peering



