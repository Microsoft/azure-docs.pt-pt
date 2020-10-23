---
title: 'Azure ExpressRoute: Projetar para alta disponibilidade'
description: Esta página fornece recomendações arquitetónicas para alta disponibilidade durante a utilização do Azure ExpressRoute.
services: expressroute
author: duongau
ms.service: expressroute
ms.topic: article
ms.date: 06/28/2019
ms.author: duau
ms.openlocfilehash: 3602c3944e8731263fbb55f024c276783950329f
ms.sourcegitcommit: 28c5fdc3828316f45f7c20fc4de4b2c05a1c5548
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/22/2020
ms.locfileid: "92202366"
---
# <a name="designing-for-high-availability-with-expressroute"></a>Projetar para alta disponibilidade com ExpressRoute

O ExpressRoute foi concebido para uma elevada disponibilidade para fornecer conectividade de rede privada de nível de transportadora aos recursos da Microsoft. Por outras palavras, não existe um único ponto de falha no caminho ExpressRoute dentro da rede microsoft. Para maximizar a disponibilidade, o cliente e o segmento de prestador de serviços do seu circuito ExpressRoute também devem ser projetados para uma elevada disponibilidade. Neste artigo, vamos primeiro analisar considerações de arquitetura de rede para construir uma conectividade de rede robusta usando um ExpressRoute, em seguida, vamos olhar para as funcionalidades de afinação que o ajudam a melhorar a alta disponibilidade do seu circuito ExpressRoute.

>[!NOTE]
>Os conceitos descritos neste artigo aplicam-se igualmente quando um circuito ExpressRoute é criado sob WAN virtual ou fora dele.
>

## <a name="architecture-considerations"></a>Considerações de arquitetura

A seguinte figura ilustra a forma recomendada de se ligar utilizando um circuito ExpressRoute para maximizar a disponibilidade de um circuito ExpressRoute.

 [![11]][1]

Para uma elevada disponibilidade, é essencial manter a redundância do circuito ExpressRoute em toda a rede de ponta a ponta. Por outras palavras, precisa de manter a redundância dentro da sua rede de serviços e não deve comprometer a redundância dentro da sua rede de prestadores de serviços. Manter o despedimento no mínimo implica evitar um único ponto de falhas na rede. Ter energia e arrefecimento redundantes para os dispositivos de rede melhorará ainda mais a elevada disponibilidade.

### <a name="first-mile-physical-layer-design-considerations"></a>Considerações de design de camada física de primeira milha

 Se terminar as ligações primárias e secundárias de um circuito ExpressRoute no mesmo Equipamento de Instalações do Cliente (CPE), está a comprometer a elevada disponibilidade dentro da sua rede no local. Além disso, se configurar as ligações primárias e secundárias através da mesma porta de um CPE (quer terminando as duas ligações sob diferentes subinterférias, quer através da fusão das duas ligações dentro da rede de parceiros), está a forçar o parceiro a comprometer também a elevada disponibilidade no seu segmento de rede. Este compromisso é ilustrado no seguinte número.

[![2]][2]

Por outro lado, se terminar as ligações primárias e secundárias de um circuito ExpressRoute em diferentes localizações geográficas, então poderá estar a comprometer o desempenho da rede da conectividade. Se o tráfego for ativamente equilibrado em todas as ligações primárias e secundárias que são encerradas em diferentes localizações geográficas, uma potencial diferença substancial na latência da rede entre os dois caminhos resultaria num desempenho sub-ideal da rede. 

Para considerações de conceção geo-redundante, consulte [Design para recuperação de desastres com o ExpressRoute][DR].

### <a name="active-active-connections"></a>Conexões ativas

A rede Microsoft está configurada para operar as ligações primárias e secundárias dos circuitos ExpressRoute em modo ativo. No entanto, através dos anúncios de rota, pode forçar as ligações redundantes de um circuito ExpressRoute a operar em modo passivo ativo. A publicidade de rotas mais específicas e a premiscção do percurso do BGP AS são as técnicas comuns utilizadas para tornar um caminho preferido em relação ao outro.

Para melhorar a elevada disponibilidade, recomenda-se operar ambas as ligações de um circuito ExpressRoute em modo ativo. Se deixar que as ligações funcionem em modo ativo, a rede da Microsoft carregará o equilíbrio entre as ligações por fluxo.

A execução das ligações primárias e secundárias de um circuito ExpressRoute em modo activo-passivo enfrenta o risco de ambas as ligações falharem na sequência de uma falha no caminho ativo. As causas comuns para a insucesso na comutação são a falta de gestão ativa da ligação passiva e a publicidade passiva de publicidade a rotas velhas.

Em alternativa, a execução das ligações primárias e secundárias de um circuito ExpressRoute em modo ativo, resulta em apenas cerca de metade dos fluxos falhando e reencaminhando, na sequência de uma falha de ligação ExpressRoute. Assim, o modo ativo irá ajudar significativamente a melhorar o tempo médio de recuperação (MTTR).

### <a name="nat-for-microsoft-peering"></a>NAT para olhando a Microsoft 

O espreitamento da Microsoft é projetado para a comunicação entre os pontos finais públicos. Assim, normalmente, os pontos finais privados no local são Network Address Translated (NATed) com IP público na rede de clientes ou parceiros antes de comunicarem sobre o espreitar da Microsoft. Assumindo que utiliza as ligações primárias e secundárias no modo ativo, onde e como o NAT tem um impacto na rapidez com que se recupera após uma falha numa das ligações ExpressRoute. Duas opções NAT diferentes são ilustradas na seguinte figura:

[![3]][3]

Na opção 1, o NAT é aplicado após a divisão do tráfego entre as ligações primária e secundária do ExpressRoute. Para satisfazer os requisitos imponentes da NAT, são utilizadas piscinas NAT independentes entre os dispositivos primário e secundário, de modo a que o tráfego de retorno chegue ao mesmo dispositivo de borda através do qual o fluxo foi escasseado.

Na opção 2, é utilizada uma piscina NAT comum antes de dividir o tráfego entre as ligações primária e secundária do ExpressRoute. É importante fazer a distinção de que a piscina nat comum antes de dividir o tráfego não significa introduzir um único ponto de falha, comprometendo assim a alta disponibilidade.

Com a opção 1, na sequência de uma falha de ligação ExpressRoute, a capacidade de alcançar a piscina NAT correspondente é quebrada. Por conseguinte, todos os fluxos quebrados devem ser restabelecidos quer por TCP quer por camada de aplicação, seguindo o tempo limite de janela correspondente. Se uma das piscinas NAT for utilizada para fazer frente a qualquer um dos servidores no local e se a conectividade correspondente falhar, os servidores no local não podem ser alcançados a partir do Azure até que a conectividade seja fixada.

Enquanto que com a opção 2, o NAT é alcançável mesmo após uma falha de ligação primária ou secundária. Portanto, a própria camada de rede pode redirecionar os pacotes e ajudar a uma recuperação mais rápida após a falha. 

> [!NOTE]
> Se utilizar a opção NAT 1 (piscinas NAT independentes para ligações ExpressRoute primárias e secundárias) e mapear uma porta de um endereço IP de um dos pools NAT para um servidor no local, o servidor não será acessível através do circuito ExpressRoute quando a ligação correspondente falhar.
> 

## <a name="fine-tuning-features-for-private-peering"></a>Características de afinação fina para espreitar privadamente

Nesta secção, revejamos as funcionalidades opcionais (dependendo da sua implementação do Azure e da sensibilidade que tem com o MTTR) que ajudam a melhorar a alta disponibilidade do seu circuito ExpressRoute. Especificamente, vamos rever a implementação consciente da zona de gateways de rede virtual ExpressRoute e deteção bidirecional de encaminhamento (BFD).

### <a name="availability-zone-aware-expressroute-virtual-network-gateways"></a>Zona de disponibilidade consciente de gateways de rede virtual ExpressRoute

Uma Zona de Disponibilidade numa região de Azure é uma combinação de um domínio de falha e um domínio de atualização. Se optar pela implementação do Azure IaaS redundante na zona, também poderá querer configurar portas de rede virtuais redundantes que terminam o espremiamento privado ExpressRoute. Para saber mais, consulte [sobre as portas de rede virtuais redundantes em zonas de disponibilidade de Azure.][zone redundant vgw] Para configurar o gateway de rede virtual redundante da zona, consulte [criar uma porta de entrada de rede virtual redundante em Zonas de Disponibilidade Azure][conf zone redundant vgw].

### <a name="improving-failure-detection-time"></a>Melhorar o tempo de deteção de falhas

O ExpressRoute apoia o BFD em relação ao espreitamento privado. O BFD reduz o tempo de deteção da falha na rede Layer 2 entre o Microsoft Enterprise Edge (MSEEs) e os seus vizinhos BGP no lado do local de cerca de 3 minutos (padrão) para menos de um segundo. O tempo de deteção de falhas rápidas ajuda a acelerar a recuperação da falha. Para saber mais, consulte [configure BFD sobre ExpressRoute][BFD].

## <a name="next-steps"></a>Passos seguintes

Neste artigo, discutimos como projetar para a alta disponibilidade de uma conectividade do circuito ExpressRoute. Um ponto de observação do circuito ExpressRoute é fixado a uma localização geográfica e, portanto, pode ser impactado por uma falha catastrófica que afeta toda a localização. 

Para considerações de design para construir conectividade de rede geo-redundante para a espinha dorsal da Microsoft que pode resistir a falhas catastróficas, que impactam toda uma região, ver [Design para recuperação de desastres com o expressRoute private peering][DR].

<!--Image References-->
[1]: ./media/designing-for-high-availability-with-expressroute/exr-reco.png "Forma recomendada de ligar através do ExpressRoute"
[2]: ./media/designing-for-high-availability-with-expressroute/suboptimal-lastmile-connectivity.png "Conectividade sub-ideais de última milha"
[3]: ./media/designing-for-high-availability-with-expressroute/nat-options.png "opções NAT"


<!--Link References-->
[zone redundant vgw]: ../vpn-gateway/about-zone-redundant-vnet-gateways.md
[conf zone redundant vgw]: ../vpn-gateway/create-zone-redundant-vnet-gateway.md
[Configure Global Reach]: ./expressroute-howto-set-global-reach.md
[BFD]: ./expressroute-bfd.md
[DR]: ./designing-for-disaster-recovery-with-expressroute-privatepeering.md