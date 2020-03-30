---
title: 'Azure ExpressRoute: Design para recuperação de desastres'
description: Esta página fornece recomendações arquitetónicas para a recuperação de desastres enquanto utiliza o Azure ExpressRoute.
services: expressroute
author: rambk
ms.service: expressroute
ms.topic: article
ms.date: 05/25/2019
ms.author: rambala
ms.openlocfilehash: 726a014983c0da959d72b7976fef2ebb2c6e9b9e
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/27/2020
ms.locfileid: "74076693"
---
# <a name="designing-for-disaster-recovery-with-expressroute-private-peering"></a>Projetando para recuperação de desastres com o peering privado ExpressRoute

O ExpressRoute foi concebido para uma elevada disponibilidade para fornecer conectividade de rede privada de qualidade transportadora aos recursos da Microsoft. Por outras palavras, não existe um único ponto de falha na rota ExpressRoute dentro da rede Microsoft. Para considerações de design para maximizar a disponibilidade de um circuito ExpressRoute, consulte [Design para alta disponibilidade com expressRoute][HA].

No entanto, tomando o popular ditado de Murphy-*se alguma coisa pode correr mal, vai*-- em consideração, neste artigo vamos focar-nos em soluções que vão além de falhas que podem ser abordadas usando um único circuito ExpressRoute. Por outras palavras, neste artigo vamos analisar considerações de arquitetura de rede para construir uma conectividade robusta da rede de backend para a recuperação de desastres utilizando circuitos geo-redundantes da ExpressRoute.

## <a name="need-for-redundant-connectivity-solution"></a>Necessidade de solução de conectividade redundante

Existem possibilidades e casos em que todo um serviço regional (seja o da Microsoft, fornecedores de serviços de rede, clientes ou outros fornecedores de serviços na nuvem) se degrada. A causa principal para tal impacto regional do serviço inclui a calamidade natural. Por conseguinte, para a continuidade das empresas e aplicações críticas da missão, é importante planear a recuperação de desastres.   

Independentemente de executar as suas aplicações críticas de missão numa região do Azure ou no local ou em qualquer outro lugar, pode usar outra região azure como seu local de failover. Os seguintes artigos abordam a recuperação de desastres das aplicações e perspetivas de acesso frontal:

- [Recuperação após desastre de dimensão empresarial][Enterprise DR]
- [Recuperação após desastre para PMEs com o Azure Site Recovery][SMB DR]

Se você confiar na conectividade ExpressRoute entre a sua rede no local e a Microsoft para operações críticas da missão, o seu plano de recuperação de desastres também deve incluir conectividade de rede geo-redundante. 

## <a name="challenges-of-using-multiple-expressroute-circuits"></a>Desafios da utilização de vários circuitos ExpressRoute

Quando interliga o mesmo conjunto de redes utilizando mais do que uma ligação, introduz caminhos paralelos entre as redes. Caminhos paralelos, quando não devidamente arquitetados, podem levar a encaminhamento assimétrico. Se tiver entidades imponentes (por exemplo, NAT, firewall) no caminho, o encaminhamento assimétrico pode bloquear o fluxo de tráfego.  Normalmente, ao longo do caminho de espreitar privado ExpressRoute não se depara com entidades audais como O NAT ou firewalls. Por conseguinte, o encaminhamento assimétrico sobre o peering privado ExpressRoute não bloqueia necessariamente o fluxo de tráfego.
 
No entanto, se carregar o tráfego de equilíbrio em caminhos paralelos geo-redundantes, independentemente de ter ou não entidades imponentes, experimentará um desempenho de rede inconsistente. Neste artigo, vamos discutir como lidar com estes desafios.

## <a name="small-to-medium-on-premises-network-considerations"></a>Considerações de rede de pequena a média no local

Vamos considerar o exemplo da rede ilustrada no diagrama seguinte. No exemplo, a conectividade ExpressRoute geo-redundante é estabelecida entre a localização de um Contoso no local e a VNet de Contoso numa região de Azure. No diagrama, a linha verde sólida indica o caminho preferido (via ExpressRoute 1) e a pontilhada representa o caminho stand-by (via ExpressRoute 2).

[![1]][1]

Quando está a desenhar a conectividade ExpressRoute para a recuperação de desastres, tem de considerar:

- utilizando circuitos geo-redundantes da ExpressRoute
- utilização de diversas redes de prestadores de serviços para diferentes circuitos ExpressRoute
- projetando cada um do circuito ExpressRoute para [alta disponibilidade][HA]
- terminando o circuito ExpressRoute diferente em diferentes locais na rede de clientes

Por predefinição, se anunciar rotas de forma idêntica em todos os caminhos da ExpressRoute, o Azure irá carregar o tráfego no local em todos os caminhos da ExpressRoute utilizando o encaminhamento multi-caminho (ECMP) de igual custo.

No entanto, com os circuitos geo-redundantes da ExpressRoute, temos de ter em conta diferentes desempenhos de rede com diferentes caminhos de rede (especialmente para a latência da rede). Para obter um desempenho de rede mais consistente durante o funcionamento normal, é melhor preferir o circuito ExpressRoute que oferece a latência mínima.

Pode influenciar o Azure a preferir um circuito ExpressRoute em vez de outro utilizando uma das seguintes técnicas (listadas na ordem de eficácia):

- a publicidade de rota mais específica sobre o circuito ExpressRoute preferido em comparação com outros circuitos ExpressRoute(s)
- configurar maior peso de ligação na ligação que liga a rede virtual ao circuito expressRoute preferido
- publicitando as rotas sobre circuito ExpressRoute menos preferido com caminho mais longo (preparação AS Path)

### <a name="more-specific-route"></a>Rota mais específica

O diagrama que se segue ilustra influenciar a seleção de caminhos expressRoute utilizando um anúncio de rota mais específico. No exemplo ilustrado, a gama Contoso on-preser /24 IP é publicitada como duas gamas de endereços /25 através do caminho preferido (ExpressRoute 1) e como /24 através do caminho stand-by (ExpressRoute 2).

[![2]][2]

Como o /25 é mais específico, em comparação com o /24, o Azure enviaria o tráfego destinado a 10.1.11.0/24 via ExpressRoute 1 no estado normal. Se ambas as ligações da ExpressRoute 1 descerem, então o VNet veria o anúncio da rota 10.1.11.0/24 apenas via ExpressRoute 2; e, portanto, o circuito de espera é usado neste estado de falha.

### <a name="connection-weight"></a>Peso de ligação

A imagem que se segue ilustra a configuração do peso de uma ligação ExpressRoute através do portal Azure.

[![3]][3]

O diagrama que se segue ilustra influenciar a seleção do caminho ExpressRoute utilizando o peso da ligação. O peso de ligação padrão é 0. No exemplo abaixo, o peso da ligação para ExpressRoute 1 está configurado como 100. Quando um VNet recebe um prefixo de rota anunciado através de mais de um circuito ExpressRoute, o VNet prefere a ligação com o maior peso.

[![4]][4]

Se ambas as ligações da ExpressRoute 1 descerem, então o VNet veria o anúncio da rota 10.1.11.0/24 apenas via ExpressRoute 2; e, portanto, o circuito de espera é usado neste estado de falha.

### <a name="as-path-prepend"></a>Preparação do caminho AS

O diagrama que se segue ilustra influenciar a seleção do caminho ExpressRoute utilizando o prepend do caminho AS. No diagrama, o anúncio de rota sobre expressRoute 1 indica o comportamento padrão do eBGP. No anúncio da rota sobre o ExpressRoute 2, a ASN da rede no local está preparada adicionalmente no caminho AS da rota. Quando a mesma rota é recebida através de vários circuitos ExpressRoute, de acordo com o processo de seleção de rotas do eBGP, a VNet preferiria a rota com o caminho AS mais curto. 

[![5]][5]

Se ambas as ligações do ExpressRoute 1 descerem, então o VNet veria o anúncio da rota 10.1.11.0/24 apenas via ExpressRoute 2. Consequentemente, quanto mais tempo o caminho as se tornar irrelevante. Portanto, o circuito de espera seria usado neste estado de falha.

Utilizando qualquer uma das técnicas, se influenciar o Azure a preferir uma das suas ExpressRoute em vez de outras, também precisa de garantir que a rede no local também prefere o mesmo caminho expressRoute para o tráfego ligado ao Azure para evitar fluxos assimétricos. Tipicamente, o valor de preferência local é usado para influenciar a rede no local para preferir um circuito ExpressRoute em vez de outros. A preferência local é uma métrica interna de BGP (iBGP). A rota bGP com o valor preferencial local mais elevado é preferível.

> [!IMPORTANT]
> Quando utiliza certos circuitos ExpressRoute como stand-by, é necessário geri-los ativamente e testar periodicamente o funcionamento da falha. 
> 

## <a name="large-distributed-enterprise-network"></a>Grande rede empresarial distribuída

Quando se tem uma grande rede empresarial distribuída, é provável que tenha vários circuitos ExpressRoute. Nesta secção, vamos ver como projetar a recuperação de desastres usando os circuitos active-active ExpressRoute, sem precisar de circuitos de stand-by adicionais. 

Vamos considerar o exemplo ilustrado no diagrama a seguir. No exemplo, Contoso tem duas localizações no local ligadas a duas implantações Contoso IaaS em duas regiões azure diferentes através de circuitos ExpressRoute em dois locais diferentes de observação. 

[![6]][6]

A forma como arquitetos a recuperação de desastres tem um impacto na forma como o tráfego transversal regional para atravessar a localização (região 1/região2 para localização2/local1) é encaminhado. Vamos considerar duas diferentes arquiteturas de desastres que atravessam o tráfego de região de forma diferente.

### <a name="scenario-1"></a>Cenário 1

No primeiro cenário, vamos projetar a recuperação de desastres de tal forma que todo o tráfego entre uma região de Azure e a rede no local flui através do circuito local ExpressRoute em estado constante. Se o circuito local ExpressRoute falhar, o circuito ExpressRoute remoto é utilizado para todos os fluxos de tráfego entre o Azure e a rede no local.

O cenário 1 é ilustrado no diagrama seguinte. No diagrama, as linhas verdes indicam caminhos para o fluxo de tráfego entre as redes VNet1 e no local. As linhas azuis indicam caminhos para o fluxo de tráfego entre as redes VNet2 e no local. Linhas sólidas indicam o caminho desejado no estado estável e as linhas tracejadas indicam que a trajetória de tráfego na falha do circuito ExpressRoute correspondente que transporta fluxos de tráfego de estado constante. 

[![7]][7]

Você pode arquiteto o cenário usando o peso de conexão para influenciar VNets para preferir a ligação à localização de peering local ExpressRoute para tráfego ligado à rede no local. Para completar a solução, é necessário garantir o fluxo de tráfego inverso simétrico. Pode utilizar a preferência local na sessão do iBGP entre os seus routers BGP (em que os circuitos ExpressRoute são terminados no lado no local) para preferir um circuito ExpressRoute. A solução é ilustrada no diagrama a seguir. 

[![8]][8]

### <a name="scenario-2"></a>Cenário 2

O Cenário 2 é ilustrado no diagrama a seguir. No diagrama, as linhas verdes indicam caminhos para o fluxo de tráfego entre as redes VNet1 e no local. As linhas azuis indicam caminhos para o fluxo de tráfego entre as redes VNet2 e no local. No estado constante (linhas sólidas no diagrama), todo o tráfego entre VNets e locais no local flui através da espinha dorsal da Microsoft na maior parte do tempo, e flui através da interligação entre locais no local apenas no estado de falha (linhas pontilhadas em o diagrama) de uma Rota Expresso.

[![9]][9]

A solução é ilustrada no diagrama a seguir. Como ilustrado, pode arquiteto rindo o cenário usando rota mais específica (Opção 1) ou preparação de caminho as (Opção 2) para influenciar a seleção de caminhos VNet. Para influenciar a seleção de rotas da rede no local para o tráfego ligado ao Azure, é necessário configurar a interligação entre o local no local como menos preferível. Howe configurar a ligação de interconexão como preferível depende do protocolo de encaminhamento utilizado dentro da rede no local. Pode utilizar a preferência local com iBGP ou métrica com IGP (OSPF ou IS-IS).

[![10]][10]


## <a name="next-steps"></a>Passos seguintes

Neste artigo, discutimos como projetar para a recuperação de desastres de um circuito ExpressRoute de conectividade de peering privado. Os seguintes artigos abordam a recuperação de desastres das aplicações e perspetivas de acesso frontal:

- [Recuperação após desastre de dimensão empresarial][Enterprise DR]
- [Recuperação após desastre para PMEs com o Azure Site Recovery][SMB DR]

<!--Image References-->
[1]: ./media/designing-for-disaster-recovery-with-expressroute-pvt/one-region.pngconsiderações de rede de "pequena a média dimensão no local"
[2]: ./media/designing-for-disaster-recovery-with-expressroute-pvt/specificroute.png "influenciando a seleção de caminhos utilizando rotas mais específicas"
[3]: ./media/designing-for-disaster-recovery-with-expressroute-pvt/configure-weight.png "configuração do peso" da ligação através do portal Azure
[4]: ./media/designing-for-disaster-recovery-with-expressroute-pvt/connectionweight.png "influenciando a seleção do caminho utilizando o peso da ligação"
[5]: ./media/designing-for-disaster-recovery-with-expressroute-pvt/aspath.png "influenciando a seleção de caminhos utilizando o prepend do caminho AS"
[6]: ./media/designing-for-disaster-recovery-with-expressroute-pvt/multi-region.pnggrandes considerações de "rede distribuídas no local"
[7]: ./media/designing-for-disaster-recovery-with-expressroute-pvt/multi-region-arch1.png "cenário 1"
[8]: ./media/designing-for-disaster-recovery-with-expressroute-pvt/multi-region-sol1.png "solução de circuitos ExpressRoute activo-activo 1"
[9]: ./media/designing-for-disaster-recovery-with-expressroute-pvt/multi-region-arch2.png "cenário 2"
[10]: ./media/designing-for-disaster-recovery-with-expressroute-pvt/multi-region-sol2.png "solução de circuitos ExpressRoute activo-activo 2"

<!--Link References-->
[HA]: https://docs.microsoft.com/azure/expressroute/designing-for-high-availability-with-expressroute
[Enterprise DR]: https://azure.microsoft.com/solutions/architecture/disaster-recovery-enterprise-scale-dr/
[SMB DR]: https://azure.microsoft.com/solutions/architecture/disaster-recovery-smb-azure-site-recovery/
[con wgt]: https://docs.microsoft.com/azure/expressroute/expressroute-optimize-routing#solution-assign-a-high-weight-to-local-connection
[AS Path Pre]: https://docs.microsoft.com/azure/expressroute/expressroute-optimize-routing#solution-use-as-path-prepending





