---
title: 'Azure ExpressRoute: Monitorização, Métricas e Alertas'
description: Saiba mais sobre a monitorização, métricas e alertas Azure ExpressRoute utilizando o Azure Monitor, o balcão único para todas as métricas, alertando, registos de diagnóstico em Azure.
services: expressroute
author: duongau
ms.service: expressroute
ms.topic: how-to
ms.date: 01/11/2020
ms.author: duau
ms.openlocfilehash: 7a5da35da35b2f447256bc742681ccd7a7d403da
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/19/2021
ms.locfileid: "99091571"
---
# <a name="expressroute-monitoring-metrics-and-alerts"></a>Monitorização, métricas e alertas do ExpressRoute

Este artigo ajuda-o a compreender a monitorização, métricas e alertas ExpressRoute usando o Azure Monitor. O Azure Monitor é um stop shop para todas as métricas, alertando, registos de diagnóstico em todo o Azure.
 
>[!NOTE]
>Não é recomendável utilizar **métricas clássicas.**
>

## <a name="expressroute-metrics"></a>Métricas ExpressRoute

Para ver **Métricas,** navegue na página *Azure Monitor* e clique em *Métricas*. Para visualizar as métricas **ExpressRoute,** filtre por *circuitos ExpressRoute* tipo de recurso . Para visualizar as métricas **do Alcance Global,** filtre pelos *circuitos ExpressRoute* do tipo de recurso e selecione um recurso de circuito ExpressRoute que tenha o Alcance Global ativado. Para visualizar métricas **ExpressRoute Direct,** filtrar o Tipo de Recurso por *Portas ExpressRoute*. 

Uma vez selecionada uma métrica, a agregação padrão será aplicada. Opcionalmente, pode aplicar a divisão, que mostrará a métrica com diferentes dimensões.

### <a name="available-metrics"></a>Métricas disponíveis

|**Métrica**|**Categoria**|**Dimensões(s)**|**Recurso(s)**|
| --- | --- | --- | --- |
|Disponibilidade de ARP|Disponibilidade|<ui><li>Peer (router expressroute primário/secundário)</ui></li><ui><li> Tipo de Peering (Privado/Público/Microsoft)</ui></li>|ExpressRoute|
|Disponibilidade BGP|Disponibilidade|<ui><li> Peer (router expressroute primário/secundário)</ui></li><ui><li> Tipo de peering</ui></li>|ExpressRoute|
|BitsInPerSecond|Trânsito|<ui><li> Tipo de Peering (ExpressRoute)</ui></li><ui><li>Link (ExpressRoute Direct)</ui></li>|<li>ExpressRoute</li><li>ExpressRoute Direct</li><ui><li>Ligação ExpressRoute Gateway</ui></li>|
|BitsOutPerSecond|Trânsito| <ui><li>Tipo de Peering (ExpressRoute)</ui></li><ui><li> Link (ExpressRoute Direct) |<ui><li>ExpressRoute<ui><li>ExpressRoute Direto</ui></li><ui><li>Ligação ExpressRoute Gateway</ui></li>|
|Utilização da CPU|Desempenho| <ui><li>Instância</ui></li>|Gateway de rede virtual ExpressRoute|
|Pacotes por Segundo|Desempenho| <ui><li>Instância</ui></li>|Gateway de rede virtual ExpressRoute|
|Contagem de rotas anunciadas a pares |Disponibilidade| <ui><li>Instância</ui></li>|Gateway de rede virtual ExpressRoute|
|Conde de Rotas Aprendidas a partir de Pares |Disponibilidade| <ui><li>Instância</ui></li>|Gateway de rede virtual ExpressRoute|
|Variação da frequência das rotas |Disponibilidade| <ui><li>Instância</ui></li>|Gateway de rede virtual ExpressRoute|
|Número de VMs na Rede Virtual |Disponibilidade| N/D |Gateway de rede virtual ExpressRoute|
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

:::image type="content" source="./media/expressroute-monitoring-metrics-alerts/ermetricspeering.jpg" alt-text="métricas de circuito":::

### <a name="bits-in-and-out---metrics-per-peering"></a>Bits Dentro e Fora - Métricas por espreitar

Pode ver métricas para privados, públicos e Microsoft olhando em bits/segundo.

:::image type="content" source="./media/expressroute-monitoring-metrics-alerts/erpeeringmetrics.jpg" alt-text="métricas por espreitar":::

### <a name="bgp-availability---split-by-peer"></a>Disponibilidade BGP - Dividido por Par  

Você pode ver perto da disponibilidade em tempo real de BGP através de pares e pares (routers ExpressRoute Primário e Secundário). Este painel mostra a sessão de BGP primária para o seu olhar privado e a segunda sessão de BGP para espreitar em privado. 

:::image type="content" source="./media/expressroute-monitoring-metrics-alerts/erBgpAvailabilityMetrics.jpg" alt-text="Disponibilidade de BGP por par":::

### <a name="arp-availability---split-by-peering"></a>Disponibilidade ARP - Dividida por Peering  

Você pode ver perto da disponibilidade em tempo real de [ARP](./expressroute-troubleshooting-arp-resource-manager.md) através de pares e pares (routers ExpressRoute Primário e Secundário). Este dashboard mostra a sessão private peering ARP em ambos os pares, mas completa para baixo para a Microsoft espreitando através de espreitar. A agregação padrão (Média) foi utilizada em ambos os pares.  

:::image type="content" source="./media/expressroute-monitoring-metrics-alerts/erArpAvailabilityMetrics.jpg" alt-text="Disponibilidade de ARP por par":::

## <a name="expressroute-direct-metrics"></a>Métricas Diretas ExpressRoute

### <a name="admin-state---split-by-link"></a>Estado de Admin - Dividido por ligação

Pode ver o estado de administração para cada ligação do par de portas ExpressRoute Direct.

:::image type="content" source="./media/expressroute-monitoring-metrics-alerts/adminstate-per-link.jpg" alt-text="Estado de administração direta do ER":::

### <a name="bits-in-per-second---split-by-link"></a>Bits In Per Second - Dividido por link

Pode ver os bits por segundo em ambas as ligações do par de portas ExpressRoute Direct.

:::image type="content" source="./media/expressroute-monitoring-metrics-alerts/bits-in-per-second-per-link.jpg" alt-text="ER Partes diretas em por segundo":::

### <a name="bits-out-per-second---split-by-link"></a>Bits out Per Second - Dividido por link

Também pode ver os bits por segundo em ambas as ligações do par de portas ExpressRoute Direct.

:::image type="content" source="./media/expressroute-monitoring-metrics-alerts/bits-out-per-second-per-link.jpg" alt-text="ER Direto bits para fora por segundo":::

### <a name="line-protocol---split-by-link"></a>Protocolo de Linha - Dividido por link

Pode ver o protocolo de linha em cada link do par de portas ExpressRoute Direct.

:::image type="content" source="./media/expressroute-monitoring-metrics-alerts/line-protocol-per-link.jpg" alt-text="Protocolo de linha direta ER":::

### <a name="rx-light-level---split-by-link"></a>Nível de Luz Rx - Dividido por ligação

Pode ver o nível de luz Rx (o nível de luz que a porta ExpressRoute Direct está **a receber)** para cada porta. Os níveis de luz Rx saudáveis geralmente se enquadram num intervalo de -10 a 0 dBm

:::image type="content" source="./media/expressroute-monitoring-metrics-alerts/rxlight-level-per-link.jpg" alt-text="Linha direta ER Nível de Luz Rx":::

### <a name="tx-light-level---split-by-link"></a>Tx Light Level - Dividido por link

Pode ver o nível de luz Tx (o nível de luz que a porta Direct ExpressRoute está **a transmitir)** para cada porta. Os níveis de luz Tx saudáveis geralmente se enquadram num intervalo de -10 a 0 dBm

:::image type="content" source="./media/expressroute-monitoring-metrics-alerts/txlight-level-per-link.jpg" alt-text="Linha direta ER Tx Nível de Luz":::

## <a name="expressroute-virtual-network-gateway-metrics"></a>Métricas de Gateway de rede virtual ExpressRoute

### <a name="cpu-utilization---split-instance"></a>Utilização do CPU - Caso dividido

Pode ver a utilização do CPU das instâncias gateway.

:::image type="content" source="./media/expressroute-monitoring-metrics-alerts/cpu-split.jpg" alt-text="Divisão do CPU":::

### <a name="packets-per-second---split-by-instance"></a>Pacotes por segundo - Dividido por Exemplo

Pode ver pacotes por segundo atravessando o portal.

:::image type="content" source="./media/expressroute-monitoring-metrics-alerts/pps-split.jpg" alt-text="Pacotes por segundo - dividido":::

### <a name="count-of-routes-advertised-to-peer---split-by-instance"></a>Contagem de rotas anunciadas a pares - Dividida por Instância

Pode ver o número de rotas anunciadas para o circuito ExpressRoute.

:::image type="content" source="./media/expressroute-monitoring-metrics-alerts/count-of-routes-advertised-to-peer.png" alt-text="Contagem de rotas anunciadas a pares":::

### <a name="count-of-routes-learned-from-peer---split-by-instance"></a>Contagem de Rotas Aprendidas a par - Dividida por Exemplo

Pode ver o número de rotas recebidas do circuito ExpressRoute.

:::image type="content" source="./media/expressroute-monitoring-metrics-alerts/count-of-routes-learned-from-peer.png" alt-text="Conde de Rotas Aprendidas a partir de Pares":::

### <a name="frequency-of-routes-change---split-by-instance"></a>Variação da frequência das rotas - Divisão por Instância

Pode ver a frequência de que a rota muda no gateway.

:::image type="content" source="./media/expressroute-monitoring-metrics-alerts/frequency-of-routes-changed.png" alt-text="Frequência das rotas alteradas":::

### <a name="number-of-vms-in-the-virtual-network"></a>Número de VMs na Rede Virtual

Pode ver o número de máquinas virtuais na rede virtual.

:::image type="content" source="./media/expressroute-monitoring-metrics-alerts/number-of-virtual-machines-virtual-network.png" alt-text="Número de máquinas virtuais na rede virtual":::

## <a name="expressroute-gateway-connections-in-bitsseconds"></a>Ligações de gateway ExpressRoute em bits/segundos

:::image type="content" source="./media/expressroute-monitoring-metrics-alerts/erconnections.jpg" alt-text="ligações de gateway":::

## <a name="alerts-for-expressroute-gateway-connections"></a>Alertas para ligações de gateway ExpressRoute

1. Para configurar alertas, navegue para **O Monitor Azure,** em seguida, selecione **Alertas**.

   :::image type="content" source="./media/expressroute-monitoring-metrics-alerts/eralertshowto.jpg" alt-text="alertas":::
2. Clique **em +Selecione Target** e selecione o recurso de ligação ao gateway ExpressRoute.

   :::image type="content" source="./media/expressroute-monitoring-metrics-alerts/alerthowto2.jpg" alt-text="alvo":::
3. Defina os detalhes do alerta.

   :::image type="content" source="./media/expressroute-monitoring-metrics-alerts/alerthowto3.jpg" alt-text="grupo de ação":::
4. Defina e adicione o grupo de ação.

   :::image type="content" source="./media/expressroute-monitoring-metrics-alerts/actiongroup.png" alt-text="adicionar grupo de ação":::

## <a name="alerts-based-on-each-peering"></a>Alertas baseados em cada observação

:::image type="content" source="./media/expressroute-monitoring-metrics-alerts/basedpeering.jpg" alt-text="cada espreitando":::

## <a name="configure-alerts-for-activity-logs-on-circuits"></a>Alertas de configuração para registos de atividades em circuitos

Nos **Critérios de Alerta,** pode selecionar **O Registo de Atividade** para o Tipo de Sinal e selecionar o Sinal.

:::image type="content" source="./media/expressroute-monitoring-metrics-alerts/alertshowto6activitylog.jpg" alt-text="registos de atividades":::

## <a name="additional-metrics-in-log-analytics"></a>Métricas adicionais em Log Analytics

Também pode ver as métricas ExpressRoute navegando para o seu recurso de circuito ExpressRoute e selecionando o separador *Logs.* Para quaisquer métricas que consultar, a saída conterá as colunas abaixo.

|**Coluna**|**Tipo**|**Descrição**|
| --- | --- | --- |
|TimeGrain|string|PT1M (os valores métricos são empurrados a cada minuto)|
|de palavras|real|Normalmente igual a 2 (cada MSEE empurra um único valor métrico a cada minuto)|
|Mínimo|real|O mínimo dos dois valores métricos empurrados pelos dois MSEEs|
|Máximo|real|O máximo dos dois valores métricos empurrados pelos dois MSEEs|
|Média|real|Igual a (Mínimo + Máximo)/2|
|Total|real|Soma dos dois valores métricos de ambos os PME (o valor principal a focar-se na métrica consultada)|
  
## <a name="next-steps"></a>Passos seguintes

Configurar a ligação do ExpressRoute.
  
* [Criar e modificar um circuito](expressroute-howto-circuit-arm.md)
* [Criar e modificar a configuração de peering](expressroute-howto-routing-arm.md)
* [Ligar uma VNet a um circuito do ExpressRoute](expressroute-howto-linkvnet-arm.md)
