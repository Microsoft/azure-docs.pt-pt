---
title: 'Azure ExpressRoute: Desenho para recuperação de desastres'
description: Esta página fornece recomendações arquitetónicas para a recuperação de desastres enquanto utiliza o Azure ExpressRoute.
services: expressroute
author: duongau
ms.service: expressroute
ms.topic: article
ms.date: 03/22/2021
ms.author: duau
ms.openlocfilehash: 3da044057784763df8d071af6c101f7baffbefc6
ms.sourcegitcommit: f0a3ee8ff77ee89f83b69bc30cb87caa80f1e724
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/26/2021
ms.locfileid: "105562312"
---
# <a name="designing-for-disaster-recovery-with-expressroute-private-peering"></a>Desenho para recuperação de desastres com o espreitamento privado ExpressRoute

O ExpressRoute foi concebido para uma elevada disponibilidade para fornecer conectividade de rede privada de nível de transportadora aos recursos da Microsoft. Por outras palavras, não há um único ponto de falha no caminho ExpressRoute dentro da rede microsoft. Para obter considerações de design para maximizar a disponibilidade de um circuito ExpressRoute, consulte [Design para alta disponibilidade com o ExpressRoute.][HA]

No entanto, tomando o adágio popular de Murphy-*se alguma coisa pode correr mal, vai*-- em consideração, neste artigo vamos focar-nos em soluções que vão além de falhas que podem ser abordadas usando um único circuito ExpressRoute. Vamos analisar considerações de arquitetura de rede para construir uma forte conectividade de rede de backend para recuperação de desastres usando circuitos ExpressRoute geo redundantes.

>[!NOTE]
>Os conceitos descritos neste artigo aplicam-se igualmente quando um circuito ExpressRoute é criado sob WAN virtual ou fora dele.
>

## <a name="need-for-redundant-connectivity-solution"></a>Necessidade de solução de conectividade redundante

Existem possibilidades e casos em que todo um serviço regional (seja o da Microsoft, fornecedores de serviços de rede, clientes ou outros prestadores de serviços na nuvem) se degrada. A causa principal para este impacto regional de serviço inclui a calamidade natural. É por isso que, para a continuidade dos negócios e aplicações críticas da missão é importante planear a recuperação de desastres.   

Não importa o que aconteça, quer você execute suas aplicações críticas de missão em uma região de Azure ou no local ou em qualquer outro lugar, você pode usar outra região de Azure como seu site de failover. Os seguintes artigos abordam a recuperação de desastres a partir de aplicações e perspetivas de acesso frontend:

- [Recuperação após desastre de dimensão empresarial][Enterprise DR]
- [Recuperação após desastre para PMEs com o Azure Site Recovery][SMB DR]

Se confia na conectividade ExpressRoute entre a sua rede no local e a Microsoft para operações críticas da missão, o seu plano de recuperação de desastres também deve incluir conectividade de rede geodussutivo. 

## <a name="challenges-of-using-multiple-expressroute-circuits"></a>Desafios da utilização de vários circuitos ExpressRoute

Quando interliga o mesmo conjunto de redes utilizando mais do que uma ligação, introduz-se caminhos paralelos entre as redes. Caminhos paralelos, quando não devidamente arquitetados, podem levar a um encaminhamento assimétrico. Se tiver entidades imponentes (por exemplo, NAT, firewall) no caminho, o encaminhamento assimétrico pode bloquear o fluxo de tráfego.  Normalmente, ao longo do caminho de observação privado ExpressRoute você não vai encontrar entidades imponentes como NAT ou Firewalls. É por isso que o encaminhamento assimétrico sobre o olho privado ExpressRoute não bloqueia necessariamente o fluxo de tráfego.
 
No entanto, se carregar o tráfego de equilíbrio através de caminhos paralelos geo-redundantes, independentemente de ter ou não entidades imponentes, experimentará um desempenho de rede inconsistente. Estes caminhos paralelos geo-redundantes podem ser através do mesmo metrô ou de um metrô diferente encontrado nos fornecedores por página [de localização.](expressroute-locations-providers.md#partners) 

### <a name="same-metro"></a>Mesmo metro

[Muitos metros](expressroute-locations-providers.md#global-commercial-azure) têm duas localizações ExpressRoute. Um exemplo seria *Amesterdão* e *Amesterdão2.* Ao projetar redundância, você poderia construir dois caminhos paralelos para Azure com ambas as localizações no mesmo metro. A vantagem deste design é quando a falha da aplicação acontece, a latência de ponta a ponta entre as aplicações no local e a Microsoft permanece aproximadamente a mesma. No entanto, se houver uma catástrofe natural, como um terramoto, a conectividade para ambos os caminhos pode deixar de estar disponível.

### <a name="different-metros"></a>Metros diferentes

Ao utilizar diferentes metros para redundância, a localização secundária deve ser na mesma [região geopolítica.](expressroute-locations-providers.md#locations) Para escolher uma localização fora da região geopolítica, você precisará usar Premium SKU para ambos os circuitos nos caminhos paralelos. A vantagem desta configuração é que as hipóteses de um desastre natural causar uma interrupção em ambas as ligações são muito mais baixas, mas à custa do aumento da latência de ponta a ponta.

Neste artigo, vamos discutir como enfrentar os desafios que pode enfrentar ao configurar caminhos geo-redundantes.

## <a name="small-to-medium-on-premises-network-considerations"></a>Considerações de rede de pequenas a médias no local

Consideremos a rede de exemplo ilustrada no diagrama seguinte. No exemplo, a conectividade ExpressRoute geo redundante é estabelecida entre a localização de um Contoso no local e a VNet de Contoso numa região do Azure. No diagrama, a linha verde sólida indica o caminho preferido (via ExpressRoute 1) e o pontilhado representa o caminho de stand-by (via ExpressRoute 2).

[![1]][1]

Ao conceber a conectividade ExpressRoute para a recuperação de desastres, tem de considerar:

- utilizando circuitos ExpressRoute geo redundantes
- utilizando redes de prestadores de serviços diversificadas para diferentes circuitos ExpressRoute
- projetando cada um dos circuitos ExpressRoute para [alta disponibilidade][HA]
- terminando o diferente circuito ExpressRoute em diferentes localizações na rede de clientes

Por padrão, se anunciar rotas idênticas em todos os caminhos ExpressRoute, a Azure irá carregar o tráfego no local ligado ao tráfego em todos os caminhos ExpressRoute utilizando o roteamento multi-caminhos de custo igual (ECMP).

No entanto, com os circuitos ExpressRoute geo redundantes, temos de ter em conta diferentes desempenhos de rede com diferentes vias de rede (especialmente para a latência da rede). Para obter um desempenho de rede mais consistente durante o funcionamento normal, pode preferir o circuito ExpressRoute que oferece a latência mínima.

Pode influenciar o Azure a preferir um circuito ExpressRoute em vez de outro utilizando uma das seguintes técnicas (listadas na ordem de eficácia):

- publicidade de rota mais específica sobre o circuito ExpressRoute preferido em comparação com outros circuitos ExpressRoute(s)
- configurar um peso de ligação mais elevado na ligação que liga a rede virtual ao circuito ExpressRoute preferido
- publicidade das rotas sobre o circuito ExpressRoute menos preferido com caminho AS mais longo (as caminhos as)

### <a name="more-specific-route"></a>Rota mais específica

O diagrama seguinte ilustra influenciar a seleção de caminhos ExpressRoute usando publicidade de rota mais específica. No exemplo ilustrado, a gama Contoso no local /24 IP é publicitada como duas /25 gamas de endereços através do caminho preferido (ExpressRoute 1) e como /24 através do caminho de stand-by (ExpressRoute 2).

[![2]][2]

Porque /25 é mais específico, comparado com /24, Azure enviaria o tráfego destinado a 10.1.11.0/24 via ExpressRoute 1 no estado normal. Se ambas as ligações do ExpressRoute 1 se descaírem, então o VNet veria o anúncio de rota 10.1.11.0/24 apenas via ExpressRoute 2; e, portanto, o circuito de espera é utilizado neste estado de avaria.

### <a name="connection-weight"></a>Peso de ligação

A imagem que se segue ilustra a configuração do peso de uma ligação ExpressRoute através do portal Azure.

[![3]][3]

O diagrama que se segue ilustra a influência na seleção do caminho ExpressRoute utilizando o peso da ligação. O peso de ligação padrão é 0. No exemplo abaixo, o peso da ligação para ExpressRoute 1 está configurado como 100. Quando um VNet recebe um prefixo de rota anunciado através de mais de um circuito ExpressRoute, o VNet preferirá a ligação com o maior peso.

[![4]][4]

Se ambas as ligações do ExpressRoute 1 se descaírem, então o VNet veria o anúncio de rota 10.1.11.0/24 apenas via ExpressRoute 2; e, portanto, o circuito de espera é utilizado neste estado de avaria.

### <a name="as-path-prepend"></a>As path prepend

O diagrama seguinte ilustra influenciar a seleção do caminho ExpressRoute utilizando o prepend do caminho AS. No diagrama, o anúncio de rota sobre o ExpressRoute 1 indica o comportamento padrão do eBGP. No anúncio de rota sobre o ExpressRoute 2, a ASN da rede no local é preparada adicionalmente no caminho AS da rota. Quando a mesma rota é recebida através de vários circuitos ExpressRoute, de acordo com o processo de seleção de rotas eBGP, a VNet preferiria a rota com o caminho AS mais curto. 

[![5]][5]

Se ambas as ligações do ExpressRoute 1 caírem, então o VNet veria o anúncio de rota 10.1.11.0/24 apenas via ExpressRoute 2. Consequentemente, o caminho mais longo do AS tornar-se-ia irrelevante. Portanto, o circuito de espera seria usado neste estado de avaria.

Utilizando qualquer uma das técnicas, se influenciar o Azure a preferir um dos seus ExpressRoute em vez de outros, também precisa de garantir que a rede no local também prefere o mesmo caminho ExpressRoute para o tráfego ligado ao Azure para evitar fluxos assimétricos. Normalmente, o valor da preferência local é usado para influenciar a rede no local para preferir um circuito ExpressRoute em vez de outros. A preferência local é uma métrica interna de BGP (iBGP). A rota BGP com o maior valor de preferência local é preferível.

> [!IMPORTANT]
> Quando utilizar certos circuitos ExpressRoute como stand-by, é necessário geri-los ativamente e testar periodicamente a operação de falha. 
> 

## <a name="large-distributed-enterprise-network"></a>Grande rede de empresas distribuídas

Quando se tem uma grande rede empresarial distribuída, é provável que tenha vários circuitos ExpressRoute. Nesta secção, vamos ver como projetar a recuperação de desastres usando os circuitos ExpressRoute ativos, sem precisar de outro circuito de stand-by. 

Vamos considerar o exemplo ilustrado no seguinte diagrama. No exemplo, o Contoso tem duas localizações no local ligadas a duas implantações do Contoso IaaS em duas regiões diferentes do Azure através dos circuitos ExpressRoute em dois locais de observação diferentes. 

[![6]][6]

A forma como arquitetamos a recuperação de desastres tem um impacto na forma como o tráfego entre regionais e localização (região1/região2 para localização2/local1) é desviado. Vamos considerar duas diferentes arquiteturas de desastres que atravessam o tráfego de localização da região de forma diferente.

### <a name="scenario-1"></a>Cenário 1

No primeiro cenário, vamos projetar a recuperação de desastres de tal forma que todo o tráfego entre uma região de Azure e rede no local flua através do circuito ExpressRoute local no estado estável. Se o circuito ExpressRoute local falhar, o circuito ExpressRoute remoto é utilizado para todos os fluxos de tráfego entre a rede Azure e no local.

O cenário 1 é ilustrado no diagrama seguinte. No diagrama, as linhas verdes indicam caminhos para o fluxo de tráfego entre as redes VNet1 e as redes no local. As linhas azuis indicam caminhos para o fluxo de tráfego entre as redes VNet2 e as redes no local. Linhas sólidas indicam o caminho desejado no estado estacioná-lo e as linhas tracejadas indicam a via de tráfego na falha do circuito ExpressRoute correspondente que transporta fluxo de tráfego de estado estável. 

[![7]][7]

Pode arquitetar o cenário usando o peso da ligação para influenciar os VNets para preferir a ligação ao local de observação ExpressRoute para o tráfego ligado à rede no local. Para completar a solução, é necessário assegurar o fluxo de tráfego inverso simétrico. Pode utilizar a preferência local na sessão iBGP entre os seus routers BGP (nos quais os circuitos ExpressRoute são encerrados no local) para preferir um circuito ExpressRoute. A solução é ilustrada no diagrama seguinte. 

[![8]][8]

### <a name="scenario-2"></a>Cenário 2

O Cenário 2 é ilustrado no diagrama seguinte. No diagrama, as linhas verdes indicam caminhos para o fluxo de tráfego entre as redes VNet1 e as redes no local. As linhas azuis indicam caminhos para o fluxo de tráfego entre as redes VNet2 e as redes no local. No estado estacional (linhas sólidas no diagrama), todo o tráfego entre VNets e locais no local flui através da espinha dorsal da Microsoft na maior parte do tempo, e flui através da interligação entre locais no local apenas no estado de avaria (linhas pontilhadas no diagrama) de um ExpressRoute.

[![9]][9]

A solução é ilustrada no diagrama seguinte. Como ilustrado, pode arquitetar o cenário utilizando rota mais específica (Opção 1) ou prepend as-path (Opção 2) para influenciar a seleção do caminho VNet. Para influenciar a seleção de rotas de rede no local para o tráfego ligado a Azure, é necessário configurar a interligação entre o local no local como menos preferível. Confige a ligação de interconexão como preferível depende do protocolo de encaminhamento utilizado dentro da rede no local. Pode utilizar a preferência local com iBGP ou métrica com IGP (OSPF ou IS-IS).

[![10]][10]


## <a name="next-steps"></a>Passos seguintes

Neste artigo, discutimos como projetar para a recuperação de desastres de um circuito ExpressRoute conectividade de espreitar privadamente. Os seguintes artigos abordam a recuperação de desastres a partir de aplicações e perspetivas de acesso frontend:

- [Recuperação após desastre de dimensão empresarial][Enterprise DR]
- [Recuperação após desastre para PMEs com o Azure Site Recovery][SMB DR]

<!--Image References-->
[1]: ./media/designing-for-disaster-recovery-with-expressroute-pvt/one-region.png "considerações de rede de pequena a média dimensão no local"
[2]: ./media/designing-for-disaster-recovery-with-expressroute-pvt/specificroute.png "influenciar a seleção de caminhos utilizando rotas mais específicas"
[3]: ./media/designing-for-disaster-recovery-with-expressroute-pvt/configure-weight.png "configurar o peso da ligação através do portal Azure"
[4]: ./media/designing-for-disaster-recovery-with-expressroute-pvt/connectionweight.png "influenciar a seleção do caminho utilizando o peso da ligação"
[5]: ./media/designing-for-disaster-recovery-with-expressroute-pvt/aspath.png "influenciando a seleção do caminho usando o prepend do caminho AS"
[6]: ./media/designing-for-disaster-recovery-with-expressroute-pvt/multi-region.png "grandes considerações distribuídas na rede"
[7]: ./media/designing-for-disaster-recovery-with-expressroute-pvt/multi-region-arch1.png "cenário 1"
[8]: ./media/designing-for-disaster-recovery-with-expressroute-pvt/multi-region-sol1.png "solução de circuitos ExpressRoute ativa 1"
[9]: ./media/designing-for-disaster-recovery-with-expressroute-pvt/multi-region-arch2.png "cenário 2"
[10]: ./media/designing-for-disaster-recovery-with-expressroute-pvt/multi-region-sol2.png "circuitos ExpressRoute ativa 2"

<!--Link References-->
[HA]: ./designing-for-high-availability-with-expressroute.md
[Enterprise DR]: https://azure.microsoft.com/solutions/architecture/disaster-recovery-enterprise-scale-dr/
[SMB DR]: https://azure.microsoft.com/solutions/architecture/disaster-recovery-smb-azure-site-recovery/
[con wgt]: ./expressroute-optimize-routing.md#solution-assign-a-high-weight-to-local-connection
[AS Path Pre]: ./expressroute-optimize-routing.md#solution-use-as-path-prepending
