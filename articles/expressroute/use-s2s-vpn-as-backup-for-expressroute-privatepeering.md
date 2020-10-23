---
title: Usando a VPN S2S como backup para a Azure ExpressRoute Private Peering Microsoft Docs
description: Esta página fornece recomendações arquitetônicas para apoiar o Azure ExpressRoute peering privado com S2S VPN.
services: networking
author: duongau
ms.service: expressroute
ms.topic: how-to
ms.date: 02/05/2020
ms.author: duau
ms.openlocfilehash: 752edea8078cf55fc3965bdc7aa9e1b4269dee34
ms.sourcegitcommit: 957c916118f87ea3d67a60e1d72a30f48bad0db6
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/19/2020
ms.locfileid: "92207925"
---
# <a name="using-s2s-vpn-as-a-backup-for-expressroute-private-peering"></a>Usando a VPN S2S como uma cópia de segurança para o espreitamento privado ExpressRoute

No artigo intitulado [Designing for disaster recovery with ExpressRoute private peering][DR-PP], discutimos a necessidade de uma solução de conectividade de backup para uma conectividade de observação privada ExpressRoute e como usar circuitos ExpressRoute geo-redundantes para o efeito. Neste artigo, consideremos como alavancar e manter a VPN site-to-site (S2S) como uma cópia de segurança para o expressRoute private peering. 

Ao contrário dos circuitos ExpressRoute geo redundantes, pode utilizar a combinação de recuperação de desastres ExpressRoute-VPN apenas em modo passivo ativo. Um grande desafio de usar qualquer conectividade de rede de backup no modo passivo é que a ligação passiva muitas vezes falharia ao lado da ligação primária. A razão comum para as falhas da ligação passiva é a falta de manutenção ativa. Portanto, neste artigo vamos focar-nos em como verificar e manter ativamente a conectividade VPN S2S que está a apoiar um peering privado ExpressRoute.

>[!NOTE] 
>Quando uma determinada rota é publicitada através do ExpressRoute e da VPN, a Azure preferiria o encaminhamento em vez do ExpressRoute.  
>

Neste artigo, vamos ver como verificar a conectividade tanto do ponto de vista Azure como da perspetiva de borda da rede do lado do cliente. A capacidade de validar a partir de qualquer das extremidades ajudará independentemente de gerir ou não os dispositivos de rede do lado do cliente que estão em pares com as entidades da rede microsoft. 

## <a name="example-topology"></a>Topologia exemplo

Na nossa configuração, temos uma rede no local ligada a um Hub VNet Azure através de um circuito ExpressRoute e uma ligação S2S VPN. O Azure hub VNet é, por sua vez, espreitado para um VNet falado, como mostrado no diagrama abaixo:

![1][1]

Na configuração, o circuito ExpressRoute é encerrado num par de routers "Customer Edge" (CE) nas instalações. O LAN no local está ligado aos routers CE através de um par de firewalls que operam no modo leader-follower. O S2S VPN é diretamente encerrado nas firewalls.

A tabela a seguir lista os prefixos IP chave da topologia:

| **Entidade** | **Prefixo** |
| --- | --- |
| Lan no local | 10.1.11.0/25 |
| Azure Hub VNet | 10.17.11.0/25 |
| Azure falou VNet | 10.17.11.128/26 |
| Servidor de teste no local | 10.1.11.10 |
| VNet testado em VNet | 10.17.11.132 |
| Sub-rede p2p de ligação primária ExpressRoute | 192.168.11.16/30 |
| Sub-rede p2p de ligação secundária ExpressRoute | 192.168.11.20/30 |
| VPN gateway principal BGP peer IP | 10.17.11.76 |
| VPN gateway secundário BGP peer IP | 10.17.11.77 |
| Ip ip peer peer VPN BGP de firewall no local | 192.168.11.88 |
| Router CE primário i/f em direção ao IP de firewall | 192.168.11.0/31 |
| Firewall i/f em direção ao IP do router CE primário | 192.168.11.1/31 |
| Router CE secundário i/f em direção ao IP de firewall | 192.168.11.2/31 |
| Firewall i/f em direção ao router CE secundário IP | 192.168.11.3/31 |


A tabela que se segue lista as ASNs da topologia:

| **Sistema autónomo** | **ASN** |
| --- | --- |
| No local | 65020 |
| Microsoft Enterprise Edge | 12076 |
| Rede Virtual GW (ExR) | 65515 |
| Rede Virtual GW (VPN) | 65515 |

## <a name="high-availability-without-asymmetricity"></a>Alta disponibilidade sem assimétrica

### <a name="configuring-for-high-availability"></a>Configuração para alta disponibilidade

[As ligações de convivência ExpressRoute e Site-to-Site discutem][Conf-CoExist] como configurar as ligações expressRoute coexisting e S2S VPN. Como discutimos em [Design para alta disponibilidade com o ExpressRoute,][HA]para melhorar a alta disponibilidade expressRoute a nossa configuração mantém a redundância de rede (evita um único ponto de falha) até aos pontos finais. Também as ligações primária e secundária dos circuitos ExpressRoute estão configuradas para funcionar em modo ativo, publicitando os prefixos no local da mesma forma através de ambas as ligações. 

A publicidade da rota no local do router CE primário através da ligação primária do circuito ExpressRoute é mostrada abaixo (comandos Junos):

```console
user@SEA-MX03-01> show route advertising-protocol bgp 192.168.11.18 

Cust11.inet.0: 8 destinations, 8 routes (7 active, 0 holddown, 1 hidden)
  Prefix                  Nexthop              MED     Lclpref    AS path
* 10.1.11.0/25            Self                                    I
```

A publicidade da rota no local do router CE secundário através da ligação secundária do circuito ExpressRoute é mostrada abaixo (comandos Junos):

```console
user@SEA-MX03-02> show route advertising-protocol bgp 192.168.11.22 

Cust11.inet.0: 8 destinations, 8 routes (7 active, 0 holddown, 1 hidden)
  Prefix                  Nexthop              MED     Lclpref    AS path
* 10.1.11.0/25            Self                                    I
```

Para melhorar a elevada disponibilidade da ligação de backup, o S2S VPN também está configurado no modo ativo. A configuração do gateway Azure VPN é mostrada abaixo. Nota como parte da configuração VPN VPN os endereços IP peer BGP do gateway -- 10.17.11.76 e 10.17.11.77 -- também estão listados.

![2][2]

A rota no local é publicitada pelas firewalls aos pares primários e secundários do BGP da porta de entrada VPN. Os anúncios de rota são mostrados abaixo (Junos):

```console
user@SEA-SRX42-01> show route advertising-protocol bgp 10.17.11.76 

Cust11.inet.0: 14 destinations, 21 routes (14 active, 0 holddown, 0 hidden)
  Prefix                  Nexthop              MED     Lclpref    AS path
* 10.1.11.0/25            Self                                    I

{primary:node0}
user@SEA-SRX42-01> show route advertising-protocol bgp 10.17.11.77    

Cust11.inet.0: 14 destinations, 21 routes (14 active, 0 holddown, 0 hidden)
  Prefix                  Nexthop              MED     Lclpref    AS path
* 10.1.11.0/25            Self                                    I
```

>[!NOTE] 
>Configurar o S2S VPN em modo ativo não só fornece alta disponibilidade para a conectividade da rede de backup de recuperação de desastres, como também fornece uma maior produção para a conectividade de backup. Por outras palavras, recomenda-se a configuração da VPN S2S em modo ativo, uma vez que força a criar múltiplos túneis subjacentes.
>

### <a name="configuring-for-symmetric-traffic-flow"></a>Configuração para o fluxo de tráfego simétrico

Notamos que quando uma determinada rota no local é publicitada através do ExpressRoute e da S2S VPN, a Azure preferiria o caminho ExpressRoute. Para forçar o Azure a preferir o caminho VPN S2S sobre o ExpressRoute coexistindo, é necessário anunciar rotas mais específicas (prefixo mais longo com máscara de sub-rede maior) através da ligação VPN. O nosso objetivo aqui é usar as ligações VPN apenas como backup. Assim, o comportamento de seleção de caminhos padrão de Azure está em consonância com o nosso objetivo. 

É nossa responsabilidade garantir que o tráfego destinado a Azure a partir do local também prefira o caminho ExpressRoute em vez da VPN S2S. A preferência local padrão dos routers ce e firewalls na nossa configuração no local é de 100. Assim, ao configurar a preferência local das rotas recebidas através dos espreitadores privados ExpressRoute superiores a 100 (digamos 150), podemos fazer com que o tráfego destinado a Azure prefira o circuito ExpressRoute no estado estável.

A configuração BGP do router CE primário que termina a ligação primária do circuito ExpressRoute é mostrada abaixo. Note-se que o valor da preferência local das rotas anunciadas durante a sessão iBGP está configurado para 150. Da mesma forma, precisamos garantir que a preferência local do router CE secundário que termina a ligação secundária do circuito ExpressRoute também está configurada para ser de 150.

```console
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
```

A tabela de encaminhamento das firewalls no local confirma (mostrada abaixo) que para o tráfego no local que está destinado a Azure o caminho preferido é sobre ExpressRoute no estado estável.

```console
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
```

Na tabela de rotas acima, para o hub e as rotas VNet faladas---10.17.11.0/25 e 10.17.11.128/26 -- vemos que o circuito ExpressRoute é preferido sobre as ligações VPN. Os 192.168.11.0 e 192.168.11.2 são IPs na interface de firewall em direção aos routers CE.

## <a name="validation-of-route-exchange-over-s2s-vpn"></a>Validação da permuta de rotas sobre a VPN S2S

No início deste artigo, verificámos no local a publicidade das firewalls aos pares primários e secundários do BGP da porta de entrada VPN. Além disso, vamos confirmar as rotas Azure recebidas pelas firewalls dos pares primários e secundários do BGP da porta de entrada VPN.

```console
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
```

Da mesma forma, vamos verificar os prefixos da rota de rede no local recebidos pelo gateway Azure VPN. 

```powershell
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
```

Como visto acima, o gateway VPN tem rotas recebidas tanto pelos pares primários e secundários do BGP da porta de entrada VPN. Tem também visibilidade sobre as rotas recebidas através de ligações expressRoute primárias e secundárias (as que têm percurso AS pré-terminada com 12076). Para confirmar as rotas recebidas através de ligações VPN, precisamos de conhecer o IP de pares BGP no local das ligações. Na nossa configuração em consideração, é 192.168.11.88 e vemos as rotas recebidas dela.

Em seguida, vamos verificar as rotas anunciadas pela porta de entrada Azure VPN para o firewall BGP peer no local (192.168.11.88).

```powershell
PS C:\Users\user> Get-AzVirtualNetworkGatewayAdvertisedRoute -Peer 192.168.11.88 -ResourceGroupName SEA-Cust11 -VirtualNetworkGatewayName SEA-Cust11-VNet01-gw-vpn |  select Network, NextHop, AsPath, Weight

Network         NextHop     AsPath Weight
-------         -------     ------ ------
10.17.11.0/25   10.17.11.76 65515       0
10.17.11.128/26 10.17.11.76 65515       0
10.17.11.0/25   10.17.11.77 65515       0
10.17.11.128/26 10.17.11.77 65515       0
```

A falta de ver trocas de rotas indica falha de ligação. Ver [Resolução de Problemas: Uma ligação VPN local-a-local não pode ligar-se e deixa de trabalhar][VPN Troubleshoot] para ajudar na resolução de problemas da ligação VPN.

## <a name="testing-failover"></a>Teste de failover

Agora que confirmamos trocas de rotas bem sucedidas sobre a ligação VPN (avião de controlo), estamos prontos para mudar o tráfego (plano de dados) da conectividade ExpressRoute para a conectividade VPN. 

>[!NOTE] 
>Nos ambientes de produção, os testes de failover têm de ser feitos durante a janela de trabalho de manutenção da rede programada, uma vez que pode ser perturbador do serviço.
>

Antes de fazer o interruptor de tráfego, vamos traçar o caminho atual na nossa configuração do servidor de teste no local para o VM de teste no VNet falado.

```console
C:\Users\PathLabUser>tracert 10.17.11.132

Tracing route to 10.17.11.132 over a maximum of 30 hops

  1    <1 ms    <1 ms    <1 ms  10.1.11.1
  2    <1 ms    <1 ms    11 ms  192.168.11.0
  3    <1 ms    <1 ms    <1 ms  192.168.11.18
  4     *        *        *     Request timed out.
  5     6 ms     6 ms     5 ms  10.17.11.132

Trace complete.
```

As subestanets de ligação ponto a ponto da nossa configuração expressRoute primárias são, respectivamente, 192.168.11.16/30 e 192.168.11.20/30. Na rota de rastreio acima, no passo 3 vemos que estamos a atingir 192.168.11.18, que é a interface IP do MSEE primário. A presença da interface MSEE confirma que, como esperado, o nosso caminho atual é sobre o ExpressRoute.

Conforme relatado no [circuito Reset ExpressRoute,][RST]vamos utilizar os seguintes comandos de powershell para desativar o espreguiçadamento primário e secundário do circuito ExpressRoute.

```powershell
$ckt = Get-AzExpressRouteCircuit -Name "expressroute name" -ResourceGroupName "SEA-Cust11"
$ckt.Peerings[0].State = "Disabled"
Set-AzExpressRouteCircuit -ExpressRouteCircuit $ckt
```

O tempo de comutação de falha depende do tempo de convergência do BGP. Na nossa configuração, o interruptor de falha demora alguns segundos (menos de 10). Após o interruptor, a repetição do percurso mostra o seguinte caminho:

```console
C:\Users\PathLabUser>tracert 10.17.11.132

Tracing route to 10.17.11.132 over a maximum of 30 hops

  1    <1 ms    <1 ms    <1 ms  10.1.11.1
  2     *        *        *     Request timed out.
  3     6 ms     7 ms     9 ms  10.17.11.132

Trace complete.
```

O resultado do traceroute confirma que a ligação de backup via S2S VPN está ativa e pode fornecer continuidade de serviço se as ligações ExpressRoute primárias e secundárias falharem. Para completar os testes de failover, vamos ativar as ligações ExpressRoute para trás e normalizar o fluxo de tráfego, utilizando o seguinte conjunto de comandos.

```powershell
$ckt = Get-AzExpressRouteCircuit -Name "expressroute name" -ResourceGroupName "SEA-Cust11"
$ckt.Peerings[0].State = "Enabled"
Set-AzExpressRouteCircuit -ExpressRouteCircuit $ckt
```

Para confirmar que o tráfego é recíproca para ExpressRoute, repita o percurso e certifique-se de que está a passar pelo espreitamento privado ExpressRoute.

## <a name="next-steps"></a>Passos seguintes

O ExpressRoute foi concebido para uma elevada disponibilidade sem um único ponto de falha dentro da rede microsoft. Ainda um circuito ExpressRoute está confinado a uma única região geográfica e a um prestador de serviços. A S2S VPN pode ser uma boa solução de backup passiva de recuperação de desastres para um circuito ExpressRoute. Para uma solução de ligação de backup passiva fiável, a manutenção regular da configuração passiva e a validação periódica da ligação são importantes. É essencial não deixar que a configuração VPN fique velha e, periodicamente (digamos, cada trimestre) repita os passos de teste de validação e failover descritos neste artigo durante a janela de manutenção.

Para ativar a monitorização e alertas com base nas métricas de gateway VPN, consulte [Configurar alertas nas métricas VPN Gateway][VPN-alerts].

Para acelerar a convergência do BGP na sequência de uma falha expressRoute, [configure o BFD sobre o ExpressRoute][BFD].

<!--Image References-->
[1]: ./media/use-s2s-vpn-as-backup-for-expressroute-privatepeering/topology.png "topologia em consideração"
[2]: ./media/use-s2s-vpn-as-backup-for-expressroute-privatepeering/vpn-gw-config.png "Configuração VPN GW"

<!--Link References-->
[DR-PP]: ./designing-for-disaster-recovery-with-expressroute-privatepeering.md
[Conf-CoExist]: ./expressroute-howto-coexist-resource-manager.md
[HA]: ./designing-for-high-availability-with-expressroute.md
[VPN Troubleshoot]: ../vpn-gateway/vpn-gateway-troubleshoot-site-to-site-cannot-connect.md
[VPN-alerts]: ../vpn-gateway/vpn-gateway-howto-setup-alerts-virtual-network-gateway-metric.md
[BFD]: ./expressroute-bfd.md
[RST]: ./expressroute-howto-reset-peering.md