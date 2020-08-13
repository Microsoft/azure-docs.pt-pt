---
title: 'Azure ExpressRoute: Monitorização, Métricas e Alertas'
description: Saiba mais sobre a monitorização, métricas e alertas Azure ExpressRoute utilizando o Azure Monitor, o balcão único para todas as métricas, alertando, registos de diagnóstico em Azure.
services: expressroute
author: mialdrid
ms.service: expressroute
ms.topic: how-to
ms.date: 08/22/2019
ms.author: cherylmc
ms.openlocfilehash: 49e5acb7fc0cfe947d846f2943fb5071d6554ea5
ms.sourcegitcommit: 9ce0350a74a3d32f4a9459b414616ca1401b415a
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 08/13/2020
ms.locfileid: "88192479"
---
# <a name="expressroute-monitoring-metrics-and-alerts"></a>Monitorização, métricas e alertas do ExpressRoute

Este artigo ajuda-o a compreender a monitorização, métricas e alertas ExpressRoute usando o Azure Monitor. O Azure Monitor é um stop shop para todas as métricas, alertando, registos de diagnóstico em todo o Azure.
 
>[!NOTE]
>Não é recomendável utilizar **métricas clássicas.**
>

## <a name="expressroute-metrics"></a>Métricas ExpressRoute

Para ver **Métricas,** navegue na página *Azure Monitor* e clique em *Métricas*. Para visualizar as métricas **ExpressRoute,** filtre por *circuitos ExpressRoute*tipo de recurso . Para visualizar as métricas **do Alcance Global,** filtre pelos *circuitos ExpressRoute* do tipo de recurso e selecione um recurso de circuito ExpressRoute que tenha o Alcance Global ativado. Para visualizar métricas **ExpressRoute Direct,** filtrar o Tipo de Recurso por *Portas ExpressRoute*. 

Uma vez selecionada uma métrica, a agregação padrão será aplicada. Opcionalmente, pode aplicar a divisão, que mostrará a métrica com diferentes dimensões.

### <a name="available-metrics"></a>Métricas disponíveis
|**Métrica**|**Categoria**|**Dimensões(s)**|**Recurso(s)**|
| --- | --- | --- | --- |
|Disponibilidade de ARP|Disponibilidade|<ui><li>Peer (router expressroute primário/secundário)</ui></li><ui><li> Tipo de Peering (Privado/Público/Microsoft)</ui></li>|ExpressRoute|
|Disponibilidade BGP|Disponibilidade|<ui><li> Peer (router expressroute primário/secundário)</ui></li><ui><li> Tipo de peering</ui></li>|ExpressRoute|
|BitsInPerSecond|Trânsito|<ui><li> Tipo de Peering (ExpressRoute)</ui></li><ui><li>Link (ExpressRoute Direct)</ui></li>| <li> ExpressRoute</li><li>ExpressRoute Direct|
|BitsOutPerSecond|Trânsito| <ui><li>Tipo de Peering (ExpressRoute)</ui></li><ui><li> Link (ExpressRoute Direct) | <ui><li>ExpressRoute<ui><li>ExpressRoute Direct</ui></li> |
|GlobalReachBitsInPerSecond|Trânsito|<ui><li>Skey de circuito esprevado (chave de serviço)</ui></li>|Alcance Global|
|GlobalReachBitsOutPerSecond|Trânsito|<ui><li>Skey de circuito esprevado (chave de serviço)</ui></li>|Alcance Global|
|Estado Administrativo|Conectividade Física|Ligação|ExpressRoute Direct|
|LineProtocol|Conectividade Física|Ligação|ExpressRoute Direct|
|RxLightLevel|Conectividade Física|<ui><li>Ligação</ui></li><ui><li>Pista</ui></li>|ExpressRoute Direct|
|TxLightLevel|Conectividade Física|<ui><li>Ligação</ui></li><ui><li>Pista</ui></li>|ExpressRoute Direct|
>[!NOTE]
>A utilização *do GlobalGlobalReachBitsInPerSecond* e *da GlobalGlobalReachBitsOutPerSecond* só será visível se for estabelecida pelo menos uma ligação Global Reach.
>

## <a name="circuits-metrics"></a>Métricas de circuitos

### <a name="bits-in-and-out---metrics-across-all-peerings"></a>Bits In and out - Métricas em todos os seus olhos

Pode ver métricas em todos os seus olhos num determinado circuito ExpressRoute.

![métricas de circuito](./media/expressroute-monitoring-metrics-alerts/ermetricspeering.jpg)

### <a name="bits-in-and-out---metrics-per-peering"></a>Bits Dentro e Fora - Métricas por espreitar

Pode ver métricas para privados, públicos e Microsoft olhando em bits/segundo.

![métricas por espreitar](./media/expressroute-monitoring-metrics-alerts/erpeeringmetrics.jpg) 

### <a name="bgp-availability---split-by-peer"></a>Disponibilidade BGP - Dividido por Par  

Você pode ver perto da disponibilidade em tempo real de BGP através de pares e pares (routers ExpressRoute Primário e Secundário). Este painel mostra a sessão de BGP primária para o seu olhar privado e a segunda sessão de BGP para espreitar em privado. 

![Disponibilidade de BGP por par](./media/expressroute-monitoring-metrics-alerts/erBgpAvailabilityMetrics.jpg) 

### <a name="arp-availability---split-by-peering"></a>Disponibilidade ARP - Dividida por Peering  

Você pode ver perto da disponibilidade em tempo real de [ARP](https://docs.microsoft.com/azure/expressroute/expressroute-troubleshooting-arp-resource-manager) através de pares e pares (routers ExpressRoute Primário e Secundário). Este dashboard mostra a sessão private peering ARP em ambos os pares, mas completa para baixo para a Microsoft espreitando através de espreitar. A agregação padrão (Média) foi utilizada em ambos os pares.  

![Disponibilidade de ARP por par](./media/expressroute-monitoring-metrics-alerts/erArpAvailabilityMetrics.jpg) 

## <a name="expressroute-direct-metrics"></a>Métricas Diretas ExpressRoute

### <a name="admin-state---split-by-link"></a>Estado de Admin - Dividido por ligação
Pode ver o estado de administração para cada ligação do par de portas ExpressRoute Direct.

![er estado de administração direta](./media/expressroute-monitoring-metrics-alerts/adminstate-per-link.jpg)

### <a name="bits-in-per-second---split-by-link"></a>Bits In Per Second - Dividido por link
Pode ver os bits por segundo em ambas as ligações do par de portas ExpressRoute Direct. 

![er bits diretos em por segundo](./media/expressroute-monitoring-metrics-alerts/bits-in-per-second-per-link.jpg)

### <a name="bits-out-per-second---split-by-link"></a>Bits out Per Second - Dividido por link
Também pode ver os bits por segundo em ambas as ligações do par de portas ExpressRoute Direct. 

![er bits diretos para fora por segundo](./media/expressroute-monitoring-metrics-alerts/bits-out-per-second-per-link.jpg)

### <a name="line-protocol---split-by-link"></a>Protocolo de Linha - Dividido por link
Pode ver o protocolo de linha em cada link do par de portas ExpressRoute Direct.

![er protocolo de linha direta](./media/expressroute-monitoring-metrics-alerts/line-protocol-per-link.jpg)

### <a name="rx-light-level---split-by-link"></a>Nível de Luz Rx - Dividido por ligação
Pode ver o nível de luz Rx (o nível de luz que a porta ExpressRoute Direct está **a receber)** para cada porta. Os níveis de luz Rx saudáveis geralmente se enquadram num intervalo de -10 a 0 dBm

![linha direta er Nível de Luz Rx](./media/expressroute-monitoring-metrics-alerts/rxlight-level-per-link.jpg)

### <a name="tx-light-level---split-by-link"></a>Tx Light Level - Dividido por link
Pode ver o nível de luz Tx (o nível de luz que a porta Direct ExpressRoute está **a transmitir)** para cada porta. Os níveis de luz Tx saudáveis geralmente se enquadram num intervalo de -10 a 0 dBm

![linha direta er Nível de Luz Rx](./media/expressroute-monitoring-metrics-alerts/txlight-level-per-link.jpg)

## <a name="expressroute-gateway-connections-in-bitsseconds"></a>Ligações de gateway ExpressRoute em bits/segundos

![ligações de gateway](./media/expressroute-monitoring-metrics-alerts/erconnections.jpg )

## <a name="alerts-for-expressroute-gateway-connections"></a>Alertas para ligações de gateway ExpressRoute

1. Para configurar alertas, navegue para **O Monitor Azure,** clique em **Alertas**.

   ![alerts](./media/expressroute-monitoring-metrics-alerts/eralertshowto.jpg)

2. Clique **em +Selecione Target** e selecione o recurso de ligação ao gateway ExpressRoute.

   ![alvo]( ./media/expressroute-monitoring-metrics-alerts/alerthowto2.jpg)
3. Defina os detalhes do alerta.

   ![grupo de ação](./media/expressroute-monitoring-metrics-alerts/alerthowto3.jpg)

4. Defina e adicione o grupo de ação.

   ![adicionar grupo de ação](./media/expressroute-monitoring-metrics-alerts/actiongroup.png)

## <a name="alerts-based-on-each-peering"></a>Alertas baseados em cada observação

 ![o que](./media/expressroute-monitoring-metrics-alerts/basedpeering.jpg)

## <a name="configure-alerts-for-activity-logs-on-circuits"></a>Alertas de configuração para registos de atividades em circuitos

Nos **Critérios de Alerta,** pode selecionar **O Registo de Atividade** para o Tipo de Sinal e selecionar o Sinal.

  ![outro](./media/expressroute-monitoring-metrics-alerts/alertshowto6activitylog.jpg)
  
## <a name="next-steps"></a>Passos seguintes

Configurar a ligação do ExpressRoute.
  
  * [Criar e modificar um circuito](expressroute-howto-circuit-arm.md)
  * [Criar e modificar a configuração de peering](expressroute-howto-routing-arm.md)
  * [Ligar uma VNet a um circuito do ExpressRoute](expressroute-howto-linkvnet-arm.md)
