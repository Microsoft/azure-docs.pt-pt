---
title: 'Azure ExpressRoute: Design para alta disponibilidade'
description: Esta página fornece recomendações arquitetónicas para alta disponibilidade enquanto utiliza o Azure ExpressRoute.
services: expressroute
author: rambk
ms.service: expressroute
ms.topic: article
ms.date: 06/28/2019
ms.author: rambala
ms.openlocfilehash: 4c3c6ae5fbdd91e6e44438be7fef2a3a91564a34
ms.sourcegitcommit: 6a4fbc5ccf7cca9486fe881c069c321017628f20
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/27/2020
ms.locfileid: "74076684"
---
# <a name="designing-for-high-availability-with-expressroute"></a>Design para alta disponibilidade com ExpressRoute

O ExpressRoute foi concebido para uma elevada disponibilidade para fornecer conectividade de rede privada de qualidade transportadora aos recursos da Microsoft. Por outras palavras, não existe um único ponto de falha na rota ExpressRoute dentro da rede Microsoft. Para maximizar a disponibilidade, o cliente e o segmento de prestador de serviços do seu circuito ExpressRoute também devem ser arquitetados para alta disponibilidade. Neste artigo, primeiro vamos analisar considerações de arquitetura de rede para construir uma conectividade robusta da rede usando uma ExpressRoute, em seguida, vamos olhar para as funcionalidades de afinação que o ajudam a melhorar a alta disponibilidade do seu circuito ExpressRoute.


## <a name="architecture-considerations"></a>Considerações de arquitetura

A figura que se segue ilustra a forma recomendada de se ligar utilizando um circuito ExpressRoute para maximizar a disponibilidade de um circuito ExpressRoute.

 [![11]][1]

Para uma elevada disponibilidade, é essencial manter a redundância do circuito ExpressRoute em toda a rede de ponta a ponta. Por outras palavras, é necessário manter o despedimento dentro da sua rede no local, e não deve comprometer a redundância dentro da sua rede de prestadores de serviços. Manter o despedimento no mínimo implica evitar um único ponto de falha na rede. Ter energia e arrefecimento redundantes para os dispositivos de rede melhorará ainda mais a elevada disponibilidade.

### <a name="first-mile-physical-layer-design-considerations"></a>Considerações de design de camada física de primeira milha

 Se terminar as ligações primárias e secundárias de um circuito ExpressRoute no mesmo Equipamento de Instalações do Cliente (CPE), está a comprometer a elevada disponibilidade dentro da sua rede no local. Além disso, se configurar as ligações primárias e secundárias através da mesma porta de um CPE (seja terminando as duas ligações em diferentes subinterfaces ou fundindo as duas ligações dentro da rede parceira), está a forçar o parceiro a comprometer também a elevada disponibilidade no seu segmento de rede. Este compromisso é ilustrado na figura seguinte.

[![2]][2]

Por outro lado, se encerrar as ligações primárias e secundárias de circuitos ExpressRoute em diferentes locais geográficos, então poderá comprometer o desempenho da rede da conectividade. Se o tráfego for ativamente carregado equilibrado em todas as ligações primárias e secundárias que são terminadas em diferentes localizações geográficas, uma potencial diferença substancial na latência da rede entre os dois caminhos resultaria num desempenho sub-ideal da rede. 

Para considerações de design geo-redundantes, consulte Design para recuperação de [desastres com expressRoute][DR].

### <a name="active-active-connections"></a>Ligações ativas

A rede Microsoft está configurada para operar as ligações primárias e secundárias dos circuitos ExpressRoute em modo ativo ativo. No entanto, através dos anúncios da sua rota, pode forçar as ligações redundantes de um circuito ExpressRoute a operar em modo passivo ativo. A publicidade de rotas mais específicas e os pré-gastos do caminho bGP AS são as técnicas comuns utilizadas para fazer um caminho preferido em relação ao outro.

Para melhorar a elevada disponibilidade, recomenda-se operar ambas as ligações de um circuito ExpressRoute em modo ativo-activo. Se deixar as ligações funcionarem em modo ativo, a rede Microsoft carregará o equilíbrio do tráfego através das ligações por caudal.

O funcionamento das ligações primárias e secundárias de um circuito ExpressRoute em modo passivo ativo enfrenta o risco de ambas as ligações falharem na sequência de uma falha no caminho ativo. As causas comuns para a não transição são a falta de gestão ativa da ligação passiva e a publicidade passiva à publicidade.

Alternativamente, executar as ligações primárias e secundárias de um circuito ExpressRoute em modo ativo ativo, resulta em apenas cerca de metade dos fluxos falhando e sendo reencaminhados, na sequência de uma falha de ligação ExpressRoute. Assim, o modo activo-activo ajudará significativamente a melhorar o tempo médio para recuperar (MTTR).

### <a name="nat-for-microsoft-peering"></a>NAT para o peering da Microsoft 

O peering da Microsoft foi concebido para a comunicação entre pontos finais públicos. Tão comumente, os pontos finais privados no local são o Endereço de Rede Traduzido (NATed) com IP público na rede de clientes ou parceiros antes de comunicarem através do peering da Microsoft. Assumindo que utiliza as ligações primárias e secundárias em modo ativo ativo, onde e como o NAT tem um impacto na rapidez com que se recupera na sequência de uma falha numa das ligações ExpressRoute. Duas opções nat diferentes são ilustradas na seguinte figura:

[![3]][3]

Na opção 1, o NAT é aplicado após dividir o tráfego entre as ligações primárias e secundárias da ExpressRoute. Para satisfazer os requisitos imponentes do NAT, são utilizadas piscinas NAT independentes entre os dispositivos primários e secundários, de modo a que o tráfego de retorno chegue ao mesmo dispositivo de borda através do qual o fluxo se esgressia.

Na opção 2, uma piscina NAT comum é usada antes de dividir o tráfego entre as ligações primárias e secundárias da ExpressRoute. É importante fazer a distinção de que a piscina comum de NAT antes de dividir o tráfego não significa introduzir um ponto de falha comprometendo assim a alta disponibilidade.

Com a opção 1, na sequência de uma falha de ligação ExpressRoute, a capacidade de alcançar a piscina NAT correspondente está quebrada. Por conseguinte, todos os fluxos partidos têm de ser restabelecidos quer pela TCP quer pela camada de aplicação, seguindo o tempo de saída correspondente. Se algum dos pools NAT for utilizado para fazer frente a qualquer um dos servidores no local e se a conectividade correspondente falhar, os servidores no local não podem ser contactados a partir de Azure até que a conectividade seja corrigida.

Considerando que, com a opção 2, o NAT é acessível mesmo após uma falha de ligação primária ou secundária. Portanto, a camada de rede em si pode redirecionar os pacotes e ajudar a recuperar mais rapidamente após a falha. 

> [!NOTE]
> Se utilizar a opção NAT 1 (piscinas NAT independentes para ligações primárias e secundárias da ExpressRoute) e mapear uma porta de um endereço IP de um dos pools NAT para um servidor no local, o servidor não será acessível através do circuito ExpressRoute quando a ligação correspondente falhar.
> 

## <a name="fine-tuning-features-for-private-peering"></a>Funcionalidades de afinação para o peering privado

Nesta secção, vamos rever opcionalmente (dependendo da sua implementação Azure e quão sensível está para o MTTR) que ajudam a melhorar a elevada disponibilidade do seu circuito ExpressRoute. Especificamente, vamos rever a implementação consciente da zona dos gateways de rede virtual ExpressRoute e a Deteção de Reencaminhamento Bidirecional (BFD).

### <a name="availability-zone-aware-expressroute-virtual-network-gateways"></a>Disponibilidade Zona consciente de Gateways de rede virtual ExpressRoute

Uma Zona de Disponibilidade numa região do Azure é uma combinação de um domínio de falha e um domínio de atualização. Se optar pela implementação do Azure IaaS redundante em zona, também poderá querer configurar gateways de rede virtual redundantes que encerram o peering privado ExpressRoute. Para saber mais, consulte [sobre os gateways de rede virtual redundantes em Zonas][zone redundant vgw]de Disponibilidade Azure . Para configurar o gateway da rede virtual redundante, consulte [Criar um portal de rede virtual redundante em Zonas][conf zone redundant vgw]de Disponibilidade Azure .

### <a name="improving-failure-detection-time"></a>Melhorar o tempo de deteção de falhas

ExpressRoute apoia bfD sobre o peering privado. O BFD reduz o tempo de deteção de falhas sobre a rede Layer 2 entre o Microsoft Enterprise Edge (MSEEs) e os seus vizinhos bGP no lado no local de cerca de 3 minutos (padrão) para menos de um segundo. O tempo de deteção rápido de falhas ajuda a acelerar a recuperação da falha. Para saber mais, consulte [configure BFD sobre expressroute][BFD].

## <a name="next-steps"></a>Passos seguintes

Neste artigo, discutimos como projetar para uma alta disponibilidade de uma conectividade de circuito ExpressRoute. Um ponto de observação do circuito ExpressRoute está fixado a uma localização geográfica e, portanto, pode ser impactado por uma falha catastrófica que afeta toda a localização. 

Para considerações de design para construir conectividade de rede geo-redundante com a espinha dorsal da Microsoft que pode suportar falhas catastróficas, que impactam toda uma região, ver Design para recuperação de [desastres com o peering privado ExpressRoute][DR].

<!--Image References-->
[1]: ./media/designing-for-high-availability-with-expressroute/exr-reco.png "Forma recomendada de se conectar usando a ExpressRoute"
[2]: ./media/designing-for-high-availability-with-expressroute/suboptimal-lastmile-connectivity.png "Conectividade sub-óptima de última milha"
[3]: ./media/designing-for-high-availability-with-expressroute/nat-options.png "opções NAT"


<!--Link References-->
[zone redundant vgw]: https://docs.microsoft.com/azure/vpn-gateway/about-zone-redundant-vnet-gateways
[conf zone redundant vgw]: https://docs.microsoft.com/azure/vpn-gateway/create-zone-redundant-vnet-gateway
[Configure Global Reach]: https://docs.microsoft.com/azure/expressroute/expressroute-howto-set-global-reach
[BFD]: https://docs.microsoft.com/azure/expressroute/expressroute-bfd
[DR]: https://docs.microsoft.com/azure/expressroute/designing-for-disaster-recovery-with-expressroute-privatepeering




