---
title: 'Azure ExpressRoute: monitoramento, métricas e alertas'
description: Esta página fornece informações sobre a monitorização de ExpressRoute
services: expressroute
author: mialdrid
ms.service: expressroute
ms.topic: conceptual
ms.date: 08/22/2019
ms.author: cherylmc
ms.openlocfilehash: 268a7e7c94285d3c4fdcb0c5fb91b685c09b58c8
ms.sourcegitcommit: f4f626d6e92174086c530ed9bf3ccbe058639081
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 12/25/2019
ms.locfileid: "75436917"
---
# <a name="expressroute-monitoring-metrics-and-alerts"></a>Monitorização, métricas e alertas do ExpressRoute

Este artigo ajuda-o a compreender o ExpressRoute, monitorização, métricas e alertas com o Azure Monitor. Monitor do Azure é um meio único para todas as métricas, alertas, os registos de diagnóstico em todas as do Azure.
 
>[!NOTE]
>Usando **métricas clássico** não é recomendado.
>

## <a name="expressroute-metrics"></a>Métricas de ExpressRoute

Para exibir as **métricas**, navegue até a página *Azure monitor* e clique em *métricas*. Para exibir as métricas de **expressroute** , filtre por tipo de recurso *circuitos de expressroute*. Para exibir **alcance global** métricas, filtre por tipo de recurso *circuitos do expressroute* e selecione um recurso de circuito do expressroute que tenha alcance global habilitado. Para exibir as métricas **diretas do expressroute** , filtre tipo de recurso por *portas do expressroute*. 

Depois que uma métrica for selecionada, a agregação padrão será aplicada. Opcionalmente, você pode aplicar a divisão, que mostrará a métrica com dimensões diferentes.

### <a name="available-metrics"></a>Métricas disponíveis
|**Métricas**|**Categoria**|**Dimensão (ões)**|**Recurso (s)**|
| --- | --- | --- | --- |
|Disponibilidade do ARP|Disponibilidade|<ui><li>Ponto (roteador de ExpressRoute primário/secundário)</ui></li><ui><li> Tipo de emparelhamento (privado/público/Microsoft)</ui></li>|ExpressRoute|
|Disponibilidade de BGP|Disponibilidade|<ui><li> Ponto (roteador de ExpressRoute primário/secundário)</ui></li><ui><li> </ui> de tipo de emparelhamento</li>|ExpressRoute|
|BitsInPerSecond|Trânsito|<ui><li> Tipo de emparelhamento (ExpressRoute)</ui></li><ui><li>Link (ExpressRoute direto)</ui></li>| <li> ExpressRoute</li><li>ExpressRoute Direct|
|BitsOutPerSecond|Trânsito| <ui><li>Tipo de emparelhamento (ExpressRoute)</ui></li><ui><li> Link (ExpressRoute direto) | <ui><li><ui> ExpressRoute<li></ui> direto do ExpressRoute</li> |
|GlobalReachBitsInPerSecond|Trânsito|<ui><li>Skey de circuito emparelhado (chave de serviço)</ui></li>|Alcance Global|
|GlobalReachBitsOutPerSecond|Trânsito|<ui><li>Skey de circuito emparelhado (chave de serviço)</ui></li>|Alcance Global|
|Adminstate|Conectividade física|Ligação|ExpressRoute Direct|
|LineProtocol|Conectividade física|Ligação|ExpressRoute Direct|
|RxLightLevel|Conectividade física|<ui><li></ui> de link</li><ui><li></ui> de Lane</li>|ExpressRoute Direct|
|TxLightLevel|Conectividade física|<ui><li></ui> de link</li><ui><li></ui> de Lane</li>|ExpressRoute Direct|
>[!NOTE]
>Usar *GlobalGlobalReachBitsInPerSecond* e *GlobalGlobalReachBitsOutPerSecond* só será visível se pelo menos uma conexão alcance global for estabelecida.
>

## <a name="circuits-metrics"></a>Métricas de circuitos

### <a name="bits-in-and-out---metrics-across-all-peerings"></a>Bits de entrada e saída-métricas em todos os emparelhamentos

Você pode exibir as métricas em todos os emparelhamentos em um determinado circuito do ExpressRoute.

![Métricas de circuito](./media/expressroute-monitoring-metrics-alerts/ermetricspeering.jpg)

### <a name="bits-in-and-out---metrics-per-peering"></a>Métricas de entrada e saída de bits por emparelhamento

Pode ver as métricas para privado, público e peering em bits por segundo da Microsoft.

![Métricas por peering](./media/expressroute-monitoring-metrics-alerts/erpeeringmetrics.jpg) 

### <a name="bgp-availability---split-by-peer"></a>Disponibilidade de BGP-divisão por par  

Você pode exibir quase a disponibilidade em tempo real do BGP entre emparelhamentos e pares (roteadores de ExpressRoute primários e secundários). Esse painel mostra a sessão BGP primária para emparelhamento privado e a segunda sessão BGP para emparelhamento privado. 

![Disponibilidade de BGP por ponto](./media/expressroute-monitoring-metrics-alerts/erBgpAvailabilityMetrics.jpg) 

### <a name="arp-availability---split-by-peering"></a>Disponibilidade ARP-divisão por emparelhamento  

Você pode exibir quase a disponibilidade em tempo real do [ARP](https://docs.microsoft.com/azure/expressroute/expressroute-troubleshooting-arp-resource-manager) entre emparelhamentos e pares (roteadores de ExpressRoute primários e secundários). Este painel mostra a sessão ARP de emparelhamento privado em ambos os pares, mas é concluída para o emparelhamento da Microsoft entre emparelhamentos. A agregação padrão (média) foi utilizada em ambos os pares.  

![Disponibilidade ARP por ponto](./media/expressroute-monitoring-metrics-alerts/erArpAvailabilityMetrics.jpg) 

## <a name="expressroute-direct-metrics"></a>Métricas diretas do ExpressRoute

### <a name="admin-state---split-by-link"></a>Estado do administrador – dividir por link
Você pode exibir o estado do administrador para cada link do par de porta do ExpressRoute Direct.

![Estado de administrador direto er](./media/expressroute-monitoring-metrics-alerts/adminstate-per-link.jpg)

### <a name="bits-in-per-second---split-by-link"></a>Bits por segundo – divisão por link
Você pode exibir os bits em por segundo em ambos os links do par de portas diretas do ExpressRoute. 

![bits diretos er em por segundo](./media/expressroute-monitoring-metrics-alerts/bits-in-per-second-per-link.jpg)

### <a name="bits-out-per-second---split-by-link"></a>Bits de saída por segundo-divisão por link
Você também pode exibir os bits de saída por segundo em ambos os links do par de portas diretas do ExpressRoute. 

![bits diretos de er de saída por segundo](./media/expressroute-monitoring-metrics-alerts/bits-out-per-second-per-link.jpg)

### <a name="line-protocol---split-by-link"></a>Protocolo de linha – dividir por link
Você pode exibir o protocolo de linha em cada link do par de porta do ExpressRoute Direct.

![Protocolo de linha direta er](./media/expressroute-monitoring-metrics-alerts/line-protocol-per-link.jpg)

### <a name="rx-light-level---split-by-link"></a>Nível de luz Rx-divisão por link
Você pode exibir o nível de luz Rx (o nível de luz que a porta do ExpressRoute Direct está **recebendo**) para cada porta. Os níveis de luz Rx em boas condições geralmente se enquadram em um intervalo de-10 a 0 dBm

![Nível de luz Rx de linha direta er](./media/expressroute-monitoring-metrics-alerts/rxlight-level-per-link.jpg)

### <a name="tx-light-level---split-by-link"></a>Nível de luz Tx – dividir por link
Você pode exibir o nível de luz Tx (o nível de luz que a porta do ExpressRoute Direct está **transmitindo**) para cada porta. Os níveis de luz Tx íntegros geralmente se enquadram dentro de um intervalo de-10 a 0 dBm

![Nível de luz Rx de linha direta er](./media/expressroute-monitoring-metrics-alerts/txlight-level-per-link.jpg)

## <a name="expressroute-gateway-connections-in-bitsseconds"></a>Ligações de gateway do ExpressRoute em bits/segundos

![ligações de gateway](./media/expressroute-monitoring-metrics-alerts/erconnections.jpg )

## <a name="alerts-for-expressroute-gateway-connections"></a>Alertas para as ligações de gateway do ExpressRoute

1. Para configurar alertas, navegue até **do Azure Monitor**, em seguida, clique em **alertas**.

   ![alerts](./media/expressroute-monitoring-metrics-alerts/eralertshowto.jpg)

2. Clique em **+ selecionar destino** e selecione o recurso de ligação de gateway do ExpressRoute.

   ![target]( ./media/expressroute-monitoring-metrics-alerts/alerthowto2.jpg)
3. Defina os detalhes do alerta.

   ![grupo de ação](./media/expressroute-monitoring-metrics-alerts/alerthowto3.jpg)

4. Definir e adicionar o grupo de ação.

   ![Adicionar grupo de ação](./media/expressroute-monitoring-metrics-alerts/actiongroup.png)

## <a name="alerts-based-on-each-peering"></a>Alertas com base em cada peering

 ![o que](./media/expressroute-monitoring-metrics-alerts/basedpeering.jpg)

## <a name="configure-alerts-for-activity-logs-on-circuits"></a>Configurar alertas para os registos de atividades em circuitos

Na **critérios de alerta**, pode selecionar **registo de atividades** para o tipo de sinal e selecione o sinal.

  ![outro](./media/expressroute-monitoring-metrics-alerts/alertshowto6activitylog.jpg)
  
## <a name="next-steps"></a>Passos seguintes

Configurar a ligação do ExpressRoute.
  
  * [Criar e modificar um circuito](expressroute-howto-circuit-arm.md)
  * [Criar e modificar a configuração de peering](expressroute-howto-routing-arm.md)
  * [Ligar uma VNet a um circuito do ExpressRoute](expressroute-howto-linkvnet-arm.md)
