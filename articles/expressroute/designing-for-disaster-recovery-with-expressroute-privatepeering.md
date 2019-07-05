---
title: Conceção para recuperação após desastre com o Azure ExpressRoute | Documentos da Microsoft
description: Esta página fornece recomendações arquiteturais para recuperação após desastre ao utilizar o Azure ExpressRoute.
documentationcenter: na
services: networking
author: rambk
manager: tracsman
ms.service: expressroute
ms.topic: article
ms.workload: infrastructure-services
ms.date: 05/25/2019
ms.author: rambala
ms.openlocfilehash: cf2b4e385de901254fde3c3d3e807feda98d5b41
ms.sourcegitcommit: c63e5031aed4992d5adf45639addcef07c166224
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 06/28/2019
ms.locfileid: "67466074"
---
# <a name="designing-for-disaster-recovery-with-expressroute-private-peering"></a>Conceção para recuperação após desastre com o peering privado do ExpressRoute

ExpressRoute foi concebido para elevada disponibilidade fornecer conectividade de rede privada de nível para recursos da Microsoft de operadora. Em outras palavras, não existe nenhum ponto único de falha no caminho do ExpressRoute na rede da Microsoft. Para considerações de design maximizar a disponibilidade de um circuito do ExpressRoute, veja [conceção para elevada disponibilidade com o ExpressRoute][HA].

No entanto, tirar populares adágio – do Murphy*se algo pode dar errado, ele será*– em consideração, neste artigo vamos nos concentrar em soluções que vão além das falhas que podem ser resolvidas utilizando um único circuito ExpressRoute. Em outras palavras, neste artigo Vamos dar uma examinada em considerações de arquitetura de rede para a criação de conectividade de rede de back-end robusto para recuperação após desastre, utilizando a circuitos do ExpressRoute com redundância geográfica.

## <a name="need-for-redundant-connectivity-solution"></a>Necessidade de solução de conectividade redundante

Há possibilidades e instâncias em que um serviço regional completo (seja que, da Microsoft, fornecedores de serviços, cliente ou outros fornecedores de serviços cloud de rede) obtém degradado. A causa de raiz para esse impacto de serviço ampla regional incluem à calamidade natural. Por conseguinte, continuidade e de missão críticos para aplicativos de negócios é importante planear a recuperação após desastre.   

Independentemente se executar seus aplicativos de missão crítica numa região do Azure ou no local ou em nenhum outro lugar, pode usar outra região do Azure como site de ativação pós-falha. A seguintes artigos endereços recuperação após desastre de aplicativos e perspectivas de acesso de front-end:

- [Recuperação após desastre de escala empresarial][Enterprise DR]
- [Recuperação após desastre para PMES com o Azure Site Recovery][SMB DR]

Se contar com conectividade do ExpressRoute entre a sua rede no local e a Microsoft para operações críticas da missão, seu plano de recuperação de desastres também deve incluir a conectividade de rede com redundância geográfica. 

## <a name="challenges-of-using-multiple-expressroute-circuits"></a>Desafios de usar vários circuitos do ExpressRoute

Quando interligar o mesmo conjunto de redes com mais de uma ligação, introduz caminhos paralelos entre as redes. Caminhos, quando não está corretamente concebido, poderia levar a encaminhamento assimétricas em paralelo. Se tiver de entidades com monitoração de estado (por exemplo, NAT, firewall) no caminho, encaminhamento assimétricas pode bloquear o fluxo de tráfego.  Normalmente, ao longo do caminho de peering privado do ExpressRoute não entram em entidades com monitoração de estado, como o NAT ou Firewalls. Por conseguinte, o encaminhamento assimétricas por peering privado do ExpressRoute não necessariamente bloqueia o fluxo de tráfego.
 
No entanto, se a carga do tráfego de balanceamento em caminhos paralelos georredundante, independentemente da existência de entidades com monitoração de estado ou não, teriam o desempenho de rede inconsistente. Neste artigo, vamos discutir como vencer esses desafios.

## <a name="small-to-medium-on-premises-network-considerations"></a>Pequenas e considerações de rede de médio no local

Vamos considerar a rede de exemplo ilustrada no diagrama seguinte. No exemplo, a conectividade do ExpressRoute com redundância geográfica é estabelecida entre a localização no local de um Contoso e VNet da Contoso na região do Azure. No diagrama, linha verde sólida indica o caminho preferido (por meio de 1 do ExpressRoute) e aquele pontilhada representa o caminho de reserva (através do ExpressRoute 2).

[![1]][1]

Durante o design de conectividade do ExpressRoute para recuperação após desastre, precisa considerar:

- utilizando a circuitos do ExpressRoute com redundância geográfica
- Utilizar rede ou redes de fornecedor de serviço diversificado de outro circuito do ExpressRoute
- criação de cada um circuito do ExpressRoute para [elevada disponibilidade][HA]
- a terminar o circuito do ExpressRoute diferente num local diferente da rede de cliente

Por predefinição, se anunciar rotas de forma idêntica ao longo de todos os caminhos do ExpressRoute, o Azure irá balancear tráfego no local ligado em todos os caminhos de ExpressRoute usando o roteamento de múltiplos caminhos (ECMP) custo igual.

No entanto, com os circuitos do ExpressRoute com redundância geográfica, precisamos considerar os desempenhos de rede diferente de consideração com caminhos de rede diferentes (especialmente para as horas de latência de rede). Para obter um desempenho de rede mais consistente durante o funcionamento normal, poderá preferir o circuito do ExpressRoute que oferece a latência mínima.

Pode influenciar o Azure para dar preferência a um circuito do ExpressRoute através de outro usando uma das técnicas seguintes (listadas por ordem de eficácia):

- o anúncio de rota mais específica através de circuito do ExpressRoute preferencial, em comparação comparado outros circuitos do ExpressRoute
- configurar o peso mais alto de ligação na ligação que liga a rede virtual para o circuito de ExpressRoute preferencial
- anuncia as rotas através do circuito de ExpressRoute menos preferencial com mais como caminho (como o caminho preceder)

### <a name="more-specific-route"></a>Rota mais específica

O diagrama seguinte ilustra a seleção de caminho influência do ExpressRoute com o anúncio de rota mais específico. No exemplo ilustrado, Contoso no local/24 intervalo IP tenha sido anunciado como intervalos de endereços de dois /25 via o caminho preferido (1 do ExpressRoute) e como /24 através do caminho reserva (2 do ExpressRoute).

[![2]][2]

Como /25 é mais específico, em comparação com /24, Azure seria enviar o tráfego destinado a 10.1.11.0/24 via 1 do ExpressRoute no estado normal. Se ambas as ligações do ExpressRoute 1 ficarem inativos, em seguida, a VNet veria o anúncio de rota 10.1.11.0/24 apenas através do ExpressRoute 2; e, portanto, o circuito de reserva dinâmica é utilizado neste estado de falha.

### <a name="connection-weight"></a>Peso de ligação

Captura de ecrã seguinte ilustra a configurar a ponderação de uma ligação do ExpressRoute através do portal do Azure.

[![3]][3]

O diagrama seguinte ilustra a seleção de caminho influência do ExpressRoute com o peso de ligação. O peso de ligação predefinido é 0. No exemplo abaixo, a ponderação da ligação para 1 do ExpressRoute está configurada como 100. Quando uma VNet recebe um prefixo de rota anunciado através de mais do que um circuito do ExpressRoute, VNet vai dar preferência a ligação com a ponderação mais elevada.

[![4]][4]

Se ambas as ligações do ExpressRoute 1 ficarem inativos, em seguida, a VNet veria o anúncio de rota 10.1.11.0/24 apenas através do ExpressRoute 2; e, portanto, o circuito de reserva dinâmica é utilizado neste estado de falha.

### <a name="as-path-prepend"></a>COMO caminho preceder

O diagrama seguinte ilustra a seleção de caminho de ExpressRoute influência dos usando como preceder do caminho. No diagrama, o anúncio de rota ao longo do ExpressRoute 1 indica o comportamento padrão do eBGP. Sobre o anúncio de rota ao longo de 2 do ExpressRoute, ASN da rede no local é anexado além da rota como caminho. Quando a mesma rota é recebida por meio de vários circuitos do ExpressRoute, pelo processo de seleção de rota de eBGP, VNet prefere a rota com o mais curto como caminho. 

[![5]][5]

Se ambas as ligações do ExpressRoute 1 ficarem inativos, em seguida, a VNet veria o anúncio de rota 10.1.11.0/24 apenas através de 2 do ExpressRoute. Consequentially, quanto mais tempo como caminho seria se tornaram irrelevante. Por conseguinte, o circuito de reserva dinâmica seria usado neste estado de falha.

Usando qualquer uma das técnicas, se influenciar do Azure para dar preferência a um do ExpressRoute através de outras pessoas, também precisa garantir que a rede no local também prefiro o mesmo caminho de ExpressRoute para o Azure vinculado tráfego para evitar fluxos assimétricos. Normalmente, o valor de preferência local é utilizado para influenciar a rede no local para dar preferência a um circuito do ExpressRoute através de outras pessoas. Preferência local é uma métrica BGP (iBGP) interna. A rota do BGP com o valor de preferência local mais alto é preferencial.

> [!IMPORTANT]
> Quando utilizar determinados circuitos do ExpressRoute como reserva, terá de os gerencie ativamente e testar periodicamente a ativação pós-falha. 
> 

## <a name="large-distributed-enterprise-network"></a>Rede de grande empresa distribuída

Quando tiver uma rede de grande empresa distribuída, o que provavelmente tem vários circuitos do ExpressRoute. Nesta secção, vamos ver como conceber a recuperação após desastre, utilizando os circuitos do ExpressRoute ativo-ativo, sem a necessidade de circuitos adicionais realçar-by. 

Consideremos o exemplo ilustrado no diagrama seguinte. No exemplo, a Contoso tem duas localizações no local ligado à implementação de IaaS de Contoso duas em duas regiões diferentes do Azure através de circuitos do ExpressRoute em duas localizações de peering diferentes. 

[![6]][6]

Como arquitetar, a recuperação após desastre tem um impacto na forma como cruzada regionais cruzar localização (region1/region2 para location2/location1) o tráfego é encaminhado. Consideremos duas arquiteturas de desastre diferentes que encaminha o tráfego de localização de região cruzada de forma diferente.

### <a name="scenario-1"></a>Cenário 1

No primeiro cenário, vamos projetar recuperação após desastre, de modo a que o fluxo de todo o tráfego entre uma rede do Azure da região e no local através de circuitos local no estado estável. Se o circuito de ExpressRoute local falhar, em seguida, o circuito de ExpressRoute remoto é utilizado para todos os fluxos de tráfego entre o Azure e rede no local.

Cenário 1 está ilustrado no diagrama seguinte. No diagrama, linhas de verde indicam caminhos para o fluxo de tráfego entre redes VNet1 e no local. As linhas azuis indicam caminhos para o fluxo de tráfego entre redes VNet2 e no local. Linhas sólidas indicam o caminho pretendido no estado de repouso e as linhas tracejadas indicam o caminho de tráfego no caso de falha do circuito de ExpressRoute correspondente, que realiza o fluxo de tráfego de estado estável. 

[![7]][7]

Pode arquitetar o cenário com o peso de ligação para influenciar VNets para dar preferência a tráfego vinculado à ligação ao local localização de peering ExpressRoute para rede no local. Para concluir a solução, terá de garantir que o fluxo de tráfego inverso simétricos. Pode utilizar preferência local na sessão iBGP entre os routers BGP (no qual os circuitos do ExpressRoute são terminados no lado no local) para dar preferência a um circuito do ExpressRoute. A solução é ilustrada no diagrama seguinte. 

[![8]][8]

### <a name="scenario-2"></a>Cenário 2

O cenário 2 é ilustrado no diagrama seguinte. No diagrama, linhas de verde indicam caminhos para o fluxo de tráfego entre redes VNet1 e no local. As linhas azuis indicam caminhos para o fluxo de tráfego entre redes VNet2 e no local. Em que o estado estável (linhas sólidas no diagrama), todos os o tráfego entre as localizações de VNets e no local através do backbone Microsoft na maior parte do fluxo e percorrem a interligação entre localizações no local apenas no estado de falha (linhas pontilhadas no o diagrama) do ExpressRoute.

[![9]][9]

A solução é ilustrada no diagrama seguinte. Conforme ilustrado, pode arquitetar o cenário de optar por utilizar mais específicas rota (opção 1) ou como caminho preceder (opção 2) para influenciar a seleção de caminho de VNet. Para influenciar a seleção de rota de rede no local para o tráfego vinculado do Azure, tem de configurar a interligação entre a localização no local como menos preferível. Como configurar a ligação de interligação como preferível depende do protocolo de encaminhamento utilizado dentro da rede no local. Pode utilizar a preferência local com iBGP ou métrica com IGP (OSPF ou IS IS).

[![10]][10]


## <a name="next-steps"></a>Passos Seguintes

Neste artigo, discutimos como estruturar para recuperação após desastre de uma conectividade de peering privada do ExpressRoute circuito. A seguintes artigos endereços recuperação após desastre de aplicativos e perspectivas de acesso de front-end:

- [Recuperação após desastre de escala empresarial][Enterprise DR]
- [Recuperação após desastre para PMES com o Azure Site Recovery][SMB DR]

<!--Image References-->
[1]: ./media/designing-for-disaster-recovery-with-expressroute-pvt/one-region.png "pequeno e considerações de rede no local de média dimensão"
[2]: ./media/designing-for-disaster-recovery-with-expressroute-pvt/specificroute.png "influenciar a seleção de caminho com rotas mais específicas"
[3]: ./media/designing-for-disaster-recovery-with-expressroute-pvt/configure-weight.png "configurar o peso de ligação através do portal do Azure"
[4]: ./media/designing-for-disaster-recovery-with-expressroute-pvt/connectionweight.png "influenciar a seleção de caminho com o peso de ligação"
[5]: ./media/designing-for-disaster-recovery-with-expressroute-pvt/aspath.png "preceder influenciar a seleção de caminho a utilizar como caminho"
[6]: ./media/designing-for-disaster-recovery-with-expressroute-pvt/multi-region.png "considerações de rede de grande distribuído no local"
[7]: ./media/designing-for-disaster-recovery-with-expressroute-pvt/multi-region-arch1.png "cenário 1"
[8]: ./media/designing-for-disaster-recovery-with-expressroute-pvt/multi-region-sol1.png "ativo-ativo ExpressRoute circuits solução 1"
[9]: ./media/designing-for-disaster-recovery-with-expressroute-pvt/multi-region-arch2.png "cenário 2"
[10]: ./media/designing-for-disaster-recovery-with-expressroute-pvt/multi-region-sol2.png "solução 2 de circuitos do ExpressRoute ativo-ativo"

<!--Link References-->
[HA]: https://docs.microsoft.com/azure/expressroute/designing-for-high-availability-with-expressroute
[Enterprise DR]: https://azure.microsoft.com/solutions/architecture/disaster-recovery-enterprise-scale-dr/
[SMB DR]: https://azure.microsoft.com/solutions/architecture/disaster-recovery-smb-azure-site-recovery/
[con wgt]: https://docs.microsoft.com/azure/expressroute/expressroute-optimize-routing#solution-assign-a-high-weight-to-local-connection
[AS Path Pre]: https://docs.microsoft.com/azure/expressroute/expressroute-optimize-routing#solution-use-as-path-prepending





