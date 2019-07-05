---
title: Conceção para elevada disponibilidade com o Azure ExpressRoute | Documentos da Microsoft
description: Esta página fornece recomendações arquiteturais para elevada disponibilidade, ao utilizar o Azure ExpressRoute.
documentationcenter: na
services: networking
author: rambk
manager: tracsman
ms.service: expressroute
ms.topic: article
ms.workload: infrastructure-services
ms.date: 06/28/2019
ms.author: rambala
ms.openlocfilehash: 4984b30daf6170873cad9472bfed2d879af57efe
ms.sourcegitcommit: c63e5031aed4992d5adf45639addcef07c166224
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 06/28/2019
ms.locfileid: "67466651"
---
# <a name="designing-for-high-availability-with-expressroute"></a>Conceção para elevada disponibilidade com o ExpressRoute

ExpressRoute foi concebido para elevada disponibilidade fornecer conectividade de rede privada de nível para recursos da Microsoft de operadora. Em outras palavras, não existe nenhum ponto único de falha no caminho do ExpressRoute na rede da Microsoft. Para maximizar a disponibilidade, o cliente e o segmento de fornecedor de serviço do seu circuito do ExpressRoute devem também ser arquitetados para elevada disponibilidade. Neste artigo, primeiro vamos examinar as considerações de arquitetura de rede para a criação de conectividade de rede robusta através do ExpressRoute, em seguida, vamos examinar as funcionalidades de ajuste fino que ajudam a melhorar a elevada disponibilidade do seu circuito do ExpressRoute.


## <a name="architecture-considerations"></a>Considerações de arquitetura

A figura seguinte ilustra a forma recomendada para ligar através de um circuito do ExpressRoute para maximizar a disponibilidade de um circuito do ExpressRoute.

 [![1]][1]

Para elevada disponibilidade, é essencial para manter a redundância do circuito ExpressRoute em toda a rede ponto a ponto. Em outras palavras, tem de manter a redundância na sua rede no local e não deve comprometer a redundância na sua rede de fornecedor de serviço. Manutenção de redundância no mínimo implica evitando ponto único de falhas de rede. Ter energia redundante e refrigeração para a rede de dispositivos serão ainda mais melhoram a elevada disponibilidade.

### <a name="first-mile-physical-layer-design-considerations"></a>Considerações de design de camada física do primeiro quilómetro

 Se encerrar tanto as ligações a primárias e secundárias de um circuito do ExpressRoute no mesmo cliente local do equipamento (CPE), está comprometer a elevada disponibilidade na sua rede no local. Além disso, se configurar ambos os as primárias e secundárias ligações via a mesma porta de uma CPE (seja ao terminar as duas conexões em sub-interfaces diferentes ou, mesclando duas conexões dentro da rede de parceiros), está forçando o parceiro para comprometer a elevada disponibilidade no respetivo segmento de rede também. Este compromisso é ilustrado na figura a seguir.

[![2]][2]

Por outro lado, se encerrar a primária e as ligações a secundárias de um circuito do ExpressRoute em localizações geográficas diferentes, em seguida, poderia ser comprometer o desempenho de rede a conectividade. Se o tráfego é ativamente o balanceamento de carga em principal e as ligações secundárias que estão terminadas em localizações geográficas diferentes, o grande potencial diferença na latência de rede entre os dois caminhos resultaria na rede inferior ao ideal desempenho. 

Para considerações de design com redundância geográfica, consulte [conceção para recuperação após desastre com o ExpressRoute][DR].

### <a name="active-active-connections"></a>Ligações ativas / ativas

Rede da Microsoft está configurada para operar as ligações a primárias e secundárias de circuitos do ExpressRoute no modo ativo-ativo. No entanto, por meio de seus anúncios de rota, pode forçar as ligações redundantes de um circuito do ExpressRoute para operar no modo ativo-passivo. Anunciar rotas mais específicas e de BGP como prefixação caminho é técnicas comuns usadas para tornar um caminho preferido em detrimento do outro.

Para melhorar a elevada disponibilidade, é recomendado para operar ambas as ligações de um circuito do ExpressRoute no modo ativo-ativo. Se permitir que as ligações funcionam num modo ativo-ativo, rede da Microsoft carregará balancear o tráfego entre as ligações numa base por fluxo.

Executar as ligações a primárias e secundárias de um circuito do ExpressRoute na face do modo ativo-passivo o risco de ambas as ligações a falhar após uma falha no caminho do Active Directory. As causas comuns de falha de alternar estão a falta de gestão ativa da ligação passiva e ligação passiva anúncio de rotas obsoletas.

Em alternativa, as ligações a primárias e secundárias de um circuito do ExpressRoute a executar no modo ativo-ativo, resulta em cerca de metade fluxos a falhar e obter reencaminhado, após uma falha de ligação de ExpressRoute. Portanto, modo ativo-ativo significativamente ajudará a melhorar o tempo médio para recuperar (MTTR).

### <a name="nat-for-microsoft-peering"></a>NAT para peering da Microsoft 

Peering da Microsoft foi concebido para a comunicação entre pontos finais públicos. Então, normalmente, pontos de extremidade privada no local são rede endereço traduzido (NATed) com um IP público ao cliente ou a rede de parceiros antes de comunicam através do peering da Microsoft. Partindo do princípio de que usar conexões principais e secundárias em modo ativo-ativo, onde e como NAT tem um impacto no rapidez recuperar após uma falha de uma das ligações do ExpressRoute. Duas diferentes opções de NAT são ilustradas na figura a seguir:

[![3]][3]

Na opção 1, NAT é aplicada após a divisão de tráfego entre as ligações primários e secundários do ExpressRoute. Para cumprir os requisitos com monitoração de estado de NAT, conjuntos NAT independentes são utilizados entre a primária e secundários dispositivos para que o tráfego de retorno deve chegar ao mesmo dispositivo edge através do qual o fluxo egressed.

A opção 2, é utilizado um conjunto comum de NAT antes de dividir o tráfego entre as ligações primários e secundários do ExpressRoute. É importante fazer a distinção que não significa que o conjunto NAT comuns antes de dividir o tráfego de introdução ao ponto único de falha, deste modo, comprometer a elevada disponibilidade.

Com a opção 1, a seguir uma falha de ligação do ExpressRoute, a capacidade de atingir o conjunto NAT correspondente foi interrompida. Portanto, todos os fluxos quebrados têm de ser novamente estabelecida por TCP ou camada de aplicativo seguindo o tempo limite da janela correspondente. Se qualquer um dos conjuntos NAT são utilizadas para front-end em qualquer um dos servidores no local e se a conectividade correspondente falhar, não não possível alcançar os servidores no local do Azure até que a conectividade é fixo.

Ao passo que com a opção 2, o NAT está acessível, mesmo após uma falha de ligação primária ou secundária. Por conseguinte, a camada de rede em si pode redirecionar os pacotes e o ajudam a recuperação mais rápida após a falha. 

> [!NOTE]
> Se utilizar a opção de NAT 1 (independentes conjuntos NAT para ligações do ExpressRoute primários e secundários) e mapeiam uma porta de um endereço IP a partir de um conjunto NAT para um servidor no local, o servidor não será acessível através do ExpressRoute circuito quando correspondente ligação falha.
> 

## <a name="fine-tuning-features-for-private-peering"></a>Funcionalidades de ajuste fino para peering privado

Nesta secção, vamos rever opcional (dependendo da sua implementação do Azure e como confidenciais que está a MTTR) recursos que ajudam a melhorar a elevada disponibilidade do seu circuito do ExpressRoute. Especificamente, vamos rever implantação com suporte para a zona de gateways de rede virtual do ExpressRoute e deteção de reencaminhamento bidirecional (BFD).

### <a name="availability-zone-aware-expressroute-virtual-network-gateways"></a>Gateways de rede virtual de ExpressRoute com reconhecimento de zona de disponibilidade

Uma zona de disponibilidade na região do Azure é uma combinação de um domínio de falha e um domínio de atualização. Se optar por implementação do IaaS do Azure com redundância de zona, pode também querer configurar gateways de rede virtual com redundância de zona que terminar o peering privado do ExpressRoute. Para saber mais, veja [sobre os gateways de rede virtual com redundância de zona em zonas de disponibilidade do Azure][zone redundant vgw]. To configure zone-redundant virtual network gateway, see [Create a zone-redundant virtual network gateway in Azure Availability Zones][conf zone redundant vgw].

### <a name="improving-failure-detection-time"></a>Melhorando o tempo de deteção de falha

O ExpressRoute suporta BFD por peering privado. BFD reduz hora da deteção de falha através da rede de camada 2 entre o limite de Enterprise da Microsoft (MSEEs) e seus vizinhos BGP no lado no local, de cerca de 3 minutos (predefinição) para menos de um segundo. Hora da deteção de falha rápida ajuda a hastening recuperação de falhas. Para saber mais, veja [BFD configurar através do ExpressRoute][BFD].

## <a name="next-steps"></a>Passos Seguintes

Neste artigo, discutimos como design de elevada disponibilidade de uma conectividade de circuito do ExpressRoute. Um ponto de peering de circuito de ExpressRoute está afixado a uma localização geográfica e, portanto, pode ser afetado por uma falha catastrófica que afeta a localização de toda. 

Para considerações de conceção para criar a conectividade de rede com redundância geográfica para backbone do Microsoft que possa suportar após falhas catastróficas, que afetam toda uma região, consulte [conceção para recuperação após desastre com o ExpressRoutedepeeringprivado][DR].

<!--Image References-->
[1]: ./media/designing-for-high-availability-with-expressroute/exr-reco.png "recomendado a forma de ligar através do ExpressRoute"
[2]: ./media/designing-for-high-availability-with-expressroute/suboptimal-lastmile-connectivity.png "Suboptimal conectividade do último quilómetro"
[3]: ./media/designing-for-high-availability-with-expressroute/nat-options.png "opções de NAT"


<!--Link References-->
[zone redundant vgw]: https://docs.microsoft.com/azure/vpn-gateway/about-zone-redundant-vnet-gateways
[conf zone redundant vgw]: https://docs.microsoft.com/azure/vpn-gateway/create-zone-redundant-vnet-gateway
[Configure Global Reach]: https://docs.microsoft.com/azure/expressroute/expressroute-howto-set-global-reach
[BFD]: https://docs.microsoft.com/azure/expressroute/expressroute-bfd
[DR]: https://docs.microsoft.com/azure/expressroute/designing-for-disaster-recovery-with-expressroute-privatepeering




