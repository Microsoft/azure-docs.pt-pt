---
title: 'Azure ExpressRoute: Monitorização, Métricas e Alertas'
description: Esta página fornece informações sobre monitorização expressRoute
services: expressroute
author: mialdrid
ms.service: expressroute
ms.topic: conceptual
ms.date: 08/22/2019
ms.author: cherylmc
ms.openlocfilehash: 268a7e7c94285d3c4fdcb0c5fb91b685c09b58c8
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/27/2020
ms.locfileid: "75436917"
---
# <a name="expressroute-monitoring-metrics-and-alerts"></a>Monitorização, métricas e alertas do ExpressRoute

Este artigo ajuda-o a compreender a monitorização, métricas e alertas do ExpressRoute utilizando o Monitor Azure. O Azure Monitor é um balcão para todas as métricas, alertando, registos de diagnóstico em todo o Azure.
 
>[!NOTE]
>Não é recomendada a utilização de **Métricas Clássicas.**
>

## <a name="expressroute-metrics"></a>Métricas ExpressRoute

Para ver **Métricas,** navegue para a página *do Monitor Azure* e clique em *Métricas*. Para ver as métricas **ExpressRoute,** filtrar por circuitos De recurso Type *ExpressRoute*. Para ver as métricas **global** reach, filtre por circuitos De recurso Type *ExpressRoute* e selecione um recurso de circuito ExpressRoute que tenha alcance global ativado. Para ver as métricas **ExpressRoute Direct,** filtrar o Tipo de Recurso por *Portas ExpressRoute*. 

Uma vez selecionada uma métrica, a agregação padrão será aplicada. Opcionalmente, pode aplicar a divisão, que mostrará a métrica com diferentes dimensões.

### <a name="available-metrics"></a>Métricas Disponíveis
|**Métricas**|**Categoria**|**Dimensão(s)**|**Características ou características**|
| --- | --- | --- | --- |
|Disponibilidade ARP|Disponibilidade|<ui><li>Peer (router Primary/Secondary ExpressRoute)</ui></li><ui><li> Tipo de observação (Privado/Público/Microsoft)</ui></li>|ExpressRoute|
|Disponibilidade de BGP|Disponibilidade|<ui><li> Peer (router Primary/Secondary ExpressRoute)</ui></li><ui><li> Tipo de observação</ui></li>|ExpressRoute|
|BitsInPerSecond|Trânsito|<ui><li> Tipo de observação (ExpressRoute)</ui></li><ui><li>Link (ExpressRoute Direct)</ui></li>| <li> ExpressRoute</li><li>ExpressRoute Direct|
|BitsOutPerSecond|Trânsito| <ui><li>Tipo de observação (ExpressRoute)</ui></li><ui><li> Link (ExpressRoute Direct) | <ui><li>ExpressRoute<ui><li>ExpressRoute Direct</ui></li> |
|GlobalReachBitsInPerSecond|Trânsito|<ui><li>Chave do circuito peered (Chave de serviço)</ui></li>|Alcance Global|
|GlobalReachBitsOutPerSecond|Trânsito|<ui><li>Chave do circuito peered (Chave de serviço)</ui></li>|Alcance Global|
|Estado-de-administração|Conectividade Física|Ligação|ExpressRoute Direct|
|Protocolo de Linha|Conectividade Física|Ligação|ExpressRoute Direct|
|Nível RxLight|Conectividade Física|<ui><li>Ligação</ui></li><ui><li>Pista</ui></li>|ExpressRoute Direct|
|TxLightLevel|Conectividade Física|<ui><li>Ligação</ui></li><ui><li>Pista</ui></li>|ExpressRoute Direct|
>[!NOTE]
>A utilização do *GlobalGlobalReachBitsInPerSecond* e *da GlobalGlobalGlobalReachBitsOutPerSecond* só será visível se for estabelecida pelo menos uma ligação Global Reach.
>

## <a name="circuits-metrics"></a>Métricas de circuitos

### <a name="bits-in-and-out---metrics-across-all-peerings"></a>Bits In and out - Métricas em todos os pares

Pode ver métricas em todos os olhares num determinado circuito ExpressRoute.

![métricas de circuito](./media/expressroute-monitoring-metrics-alerts/ermetricspeering.jpg)

### <a name="bits-in-and-out---metrics-per-peering"></a>Bits In and out - Métricas por espreitamento

Pode ver métricas para privados, públicos e Microsoft a espreitar em pedaços/segundo.

![métricas por espreitamento](./media/expressroute-monitoring-metrics-alerts/erpeeringmetrics.jpg) 

### <a name="bgp-availability---split-by-peer"></a>Disponibilidade BGP - Dividido por Pares  

Pode visualizar perto da disponibilidade em tempo real de BGP em pares e pares (routers Primary e Secondary ExpressRoute). Este painel de instrumentos mostra a sessão primária de BGP para o peering privado e a segunda sessão de BGP para o peering privado. 

![Disponibilidade de BGP por par](./media/expressroute-monitoring-metrics-alerts/erBgpAvailabilityMetrics.jpg) 

### <a name="arp-availability---split-by-peering"></a>Disponibilidade ARP - Dividido por Peering  

Pode visualizar perto da disponibilidade em tempo real de [ARP](https://docs.microsoft.com/azure/expressroute/expressroute-troubleshooting-arp-resource-manager) entre pares e pares (routers Primary e Secondary ExpressRoute). Este dashboard mostra a sessão Private Peering ARP em ambos os pares, mas completa para a Microsoft espreitar através de pares. A agregação padrão (Média) foi utilizada em ambos os pares.  

![Disponibilidade aRP por par](./media/expressroute-monitoring-metrics-alerts/erArpAvailabilityMetrics.jpg) 

## <a name="expressroute-direct-metrics"></a>Métricas Diretas ExpressRoute

### <a name="admin-state---split-by-link"></a>Estado De Administração - Dividido por link
Pode visualizar o estado de administração para cada ligação do par de portas ExpressRoute Direct.

![er estado administrativo direto](./media/expressroute-monitoring-metrics-alerts/adminstate-per-link.jpg)

### <a name="bits-in-per-second---split-by-link"></a>Bits In Per Second - Dividido por link
Pode ver os bits por segundo em ambas as ligações do par de portas ExpressRoute Direct. 

![er bits diretos em por segundo](./media/expressroute-monitoring-metrics-alerts/bits-in-per-second-per-link.jpg)

### <a name="bits-out-per-second---split-by-link"></a>Bits out Por Segundo - Dividido por link
Também pode ver os bits por segundo em ambas as ligações do par de portas ExpressRoute Direct. 

![er diretas bits para fora por segundo](./media/expressroute-monitoring-metrics-alerts/bits-out-per-second-per-link.jpg)

### <a name="line-protocol---split-by-link"></a>Protocolo de Linha - Dividido por link
Pode ver o protocolo de linha em cada link do par de portas ExpressRoute Direct.

![er protocolo de linha direta](./media/expressroute-monitoring-metrics-alerts/line-protocol-per-link.jpg)

### <a name="rx-light-level---split-by-link"></a>Nível de luz Rx - Dividido por link
Pode ver o nível de luz Rx (o nível de luz que a porta ExpressRoute Direct está **a receber)** para cada porta. Níveis de luz Rx saudáveis geralmente estão dentro de um intervalo de -10 a 0 dBm

![er linha direta Rx Light Level](./media/expressroute-monitoring-metrics-alerts/rxlight-level-per-link.jpg)

### <a name="tx-light-level---split-by-link"></a>Nível de luz Tx - Dividido por link
Pode ver o nível de luz Tx (o nível de luz que a porta ExpressRoute Direct está **a transmitir)** para cada porta. Níveis de luz Tx saudáveis geralmente estão dentro de um intervalo de -10 a 0 dBm

![er linha direta Rx Light Level](./media/expressroute-monitoring-metrics-alerts/txlight-level-per-link.jpg)

## <a name="expressroute-gateway-connections-in-bitsseconds"></a>Conexões de gateway ExpressRoute em bits/segundos

![ligações gateway](./media/expressroute-monitoring-metrics-alerts/erconnections.jpg )

## <a name="alerts-for-expressroute-gateway-connections"></a>Alertas para ligações de gateway ExpressRoute

1. Para configurar os alertas, navegue para **o Monitor Azure**e, em seguida, clique em **Alertas**.

   ![alerts](./media/expressroute-monitoring-metrics-alerts/eralertshowto.jpg)

2. Clique em **+Selecione Target** e selecione o recurso de ligação de gateway ExpressRoute.

   ![alvo]( ./media/expressroute-monitoring-metrics-alerts/alerthowto2.jpg)
3. Defina os detalhes do alerta.

   ![grupo de ação](./media/expressroute-monitoring-metrics-alerts/alerthowto3.jpg)

4. Defina e adicione o grupo de ação.

   ![adicionar grupo de ação](./media/expressroute-monitoring-metrics-alerts/actiongroup.png)

## <a name="alerts-based-on-each-peering"></a>Alertas baseados em cada espreitamento

 ![o que](./media/expressroute-monitoring-metrics-alerts/basedpeering.jpg)

## <a name="configure-alerts-for-activity-logs-on-circuits"></a>Configure alertas para registos de atividade em circuitos

Nos **Critérios**de Alerta, pode selecionar **o Registo de Atividade** para o Tipo de Sinal e selecionar o Sinal.

  ![outro](./media/expressroute-monitoring-metrics-alerts/alertshowto6activitylog.jpg)
  
## <a name="next-steps"></a>Passos seguintes

Configurar a ligação do ExpressRoute.
  
  * [Criar e modificar um circuito](expressroute-howto-circuit-arm.md)
  * [Criar e modificar a configuração de peering](expressroute-howto-routing-arm.md)
  * [Ligar uma VNet a um circuito do ExpressRoute](expressroute-howto-linkvnet-arm.md)
