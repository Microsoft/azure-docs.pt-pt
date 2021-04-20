---
title: 'Azure ExpressRoute: Monitorização, Métricas e Alertas'
description: Saiba mais sobre a monitorização, métricas e alertas Azure ExpressRoute utilizando o Azure Monitor, o balcão único para todas as métricas, alertando, registos de diagnóstico em Azure.
services: expressroute
author: duongau
ms.service: expressroute
ms.topic: how-to
ms.date: 04/07/2021
ms.author: duau
ms.openlocfilehash: 44ddf54aac61ab00009e7e2cc820b38074c5e8c3
ms.sourcegitcommit: 6f1aa680588f5db41ed7fc78c934452d468ddb84
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/19/2021
ms.locfileid: "107725787"
---
# <a name="expressroute-monitoring-metrics-and-alerts"></a>Monitorização, métricas e alertas do ExpressRoute

Este artigo ajuda-o a compreender a monitorização, métricas e alertas ExpressRoute usando o Azure Monitor. O Azure Monitor é um stop shop para todas as métricas, alertando, registos de diagnóstico em todo o Azure.
 
>[!NOTE]
>Não é recomendável utilizar **métricas clássicas.**
>

## <a name="expressroute-metrics"></a>Métricas ExpressRoute

Para ver **Métricas,** navegue na página *do Monitor Azure* e selecione *Métricas*. Para visualizar as métricas **ExpressRoute,** filtre por *circuitos ExpressRoute* tipo de recurso . Para visualizar as métricas **do Alcance Global,** filtre pelos *circuitos ExpressRoute* do tipo de recurso e selecione um recurso de circuito ExpressRoute que tenha o Alcance Global ativado. Para visualizar métricas **ExpressRoute Direct,** filtrar o Tipo de Recurso por *Portas ExpressRoute*. 

Uma vez selecionada uma métrica, a agregação padrão será aplicada. Opcionalmente, pode aplicar a divisão, que mostrará a métrica com diferentes dimensões.

### <a name="aggregation-types"></a>Tipos de agregação:

O explorador de métricas suporta SUM, MAX, MIN, AVG e COUNT como [tipos de agregação](../azure-monitor/essentials/metrics-charts.md#aggregation). Deve utilizar o tipo de agregação recomendado ao rever os insights para cada métrica ExpressRoute.

* Soma: A soma de todos os valores capturados durante o intervalo de agregação. 
* Contagem: O número de medições capturadas durante o intervalo de agregação. 
* Média: A média dos valores métricos capturados durante o intervalo de agregação. 
* Min: O menor valor capturado durante o intervalo de agregação. 
* Max: O maior valor capturado durante o intervalo de agregação. 

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

Tipo de agregação: *Avg*

Pode ver métricas em todos os seus olhos num determinado circuito ExpressRoute.

:::image type="content" source="./media/expressroute-monitoring-metrics-alerts/ermetricspeering.jpg" alt-text="métricas de circuito":::

### <a name="bits-in-and-out---metrics-per-peering"></a>Bits Dentro e Fora - Métricas por espreitar

Tipo de agregação: *Avg*

Pode ver métricas para privados, públicos e Microsoft olhando em bits/segundo.

:::image type="content" source="./media/expressroute-monitoring-metrics-alerts/erpeeringmetrics.jpg" alt-text="métricas por espreitar":::

### <a name="bgp-availability---split-by-peer"></a>Disponibilidade BGP - Dividido por Par  

Tipo de agregação: *Avg*

Você pode ver perto da disponibilidade em tempo real de BGP (conectividade Camada-3) através de pares e pares (routers ExpressRoute Primário e Secundário). Este painel de instrumentos mostra que o estado da sessão de BGP primário está em cima do par privado e o estatuto da segunda sessão de BGP está reduzido para o aconselhamento privado. 

:::image type="content" source="./media/expressroute-monitoring-metrics-alerts/erBgpAvailabilityMetrics.jpg" alt-text="Disponibilidade de BGP por par":::

### <a name="arp-availability---split-by-peering"></a>Disponibilidade ARP - Dividida por Peering  

Tipo de agregação: *Avg*

Pode ver perto da disponibilidade em tempo real de [ARP](./expressroute-troubleshooting-arp-resource-manager.md) (conectividade Camada-3) através de pares e pares (routers ExpressRoute Primários e Secundários). Este dashboard mostra que o estado da sessão private peering ARP está em todos os pares, mas para baixo para a Microsoft espreitar para ambos os pares. A agregação padrão (Média) foi utilizada em ambos os pares.  

:::image type="content" source="./media/expressroute-monitoring-metrics-alerts/erArpAvailabilityMetrics.jpg" alt-text="Disponibilidade de ARP por par":::

## <a name="expressroute-direct-metrics"></a>Métricas Diretas ExpressRoute

### <a name="admin-state---split-by-link"></a>Estado de Admin - Dividido por ligação

Tipo de agregação: *Avg*

Pode ver o estado de Administração para cada ligação do par de portas ExpressRoute Direct. O estado de Administração representa se a porta física estiver dentro ou fora. Este estado é obrigado a passar o tráfego através da ligação ExpressRoute Direct.

:::image type="content" source="./media/expressroute-monitoring-metrics-alerts/adminstate-per-link.jpg" alt-text="Estado de administração direta do ER":::

### <a name="bits-in-per-second---split-by-link"></a>Bits In Per Second - Dividido por link

Tipo de agregação: *Avg*

Pode ver os bits por segundo em ambas as ligações do par de portas ExpressRoute Direct. Monitorize este painel para comparar a largura de banda de entrada para ambas as ligações.

:::image type="content" source="./media/expressroute-monitoring-metrics-alerts/bits-in-per-second-per-link.jpg" alt-text="ER Partes diretas em por segundo":::

### <a name="bits-out-per-second---split-by-link"></a>Bits out Per Second - Dividido por link

Tipo de agregação: *Avg*

Também pode ver os bits por segundo em ambas as ligações do par de portas ExpressRoute Direct. Monitorize este painel para comparar largura de banda de saída para ambas as ligações.

:::image type="content" source="./media/expressroute-monitoring-metrics-alerts/bits-out-per-second-per-link.jpg" alt-text="ER Direto bits para fora por segundo":::

### <a name="line-protocol---split-by-link"></a>Protocolo de Linha - Dividido por link

Tipo de agregação: *Avg*

Pode ver o protocolo de linha em cada link do par de portas ExpressRoute Direct. O Protocolo de Linha indica se a ligação física está em funcionamento sobre o ExpressRoute Direct. Monitorize este painel e desapedaque os alertas para saber quando a ligação física foi avariou.

:::image type="content" source="./media/expressroute-monitoring-metrics-alerts/line-protocol-per-link.jpg" alt-text="Protocolo de linha direta ER":::

### <a name="rx-light-level---split-by-link"></a>Nível de Luz Rx - Dividido por ligação

Tipo de agregação: *Avg*

Pode ver o nível de luz Rx (o nível de luz que a porta ExpressRoute Direct está **a receber)** para cada porta. Os níveis de luz Rx saudáveis geralmente se enquadram num intervalo de -10 dBm a 0 dBm. Desaça alertas a serem notificados se o nível de luz Rx ficar fora do alcance saudável.

:::image type="content" source="./media/expressroute-monitoring-metrics-alerts/rxlight-level-per-link.jpg" alt-text="Linha direta ER Nível de Luz Rx":::

### <a name="tx-light-level---split-by-link"></a>Tx Light Level - Dividido por link

Tipo de agregação: *Avg*

Pode ver o nível de luz Tx (o nível de luz que a porta Direct ExpressRoute está **a transmitir)** para cada porta. Os níveis de luz Tx saudáveis geralmente se enquadram num intervalo de -10 dBm a 0 dBm. Desaça alertas a serem notificados se o nível de luz Tx cair fora do alcance saudável.

:::image type="content" source="./media/expressroute-monitoring-metrics-alerts/txlight-level-per-link.jpg" alt-text="Linha direta ER Tx Nível de Luz":::

## <a name="expressroute-virtual-network-gateway-metrics"></a>Métricas de Gateway de rede virtual ExpressRoute

Tipo de agregação: *Avg*

Ao implementar um gateway ExpressRoute, o Azure gere o cálculo e as funções do seu gateway. Existem seis métricas de gateway disponíveis para entender melhor o desempenho do seu portal:

* Utilização da CPU
* Pacotes por segundo
* Contagem de rotas anunciadas aos pares
* Contagem de rotas aprendidas com os pares
* Frequência das rotas alteradas
* Número de VMs na rede virtual  

É altamente recomendado que desemende alertas para cada uma destas métricas para que esteja ciente de quando o seu gateway pode estar a ver problemas de desempenho.

### <a name="cpu-utilization---split-instance"></a>Utilização do CPU - Caso dividido

Tipo de agregação: *Avg*

Pode ver a utilização do CPU de cada instância de gateway. A utilização do CPU pode aumentar brevemente durante a manutenção do hospedeiro de rotina, mas prolongar uma utilização elevada do CPU pode indicar que o seu portal está a atingir um estrangulamento de desempenho. Aumentar o tamanho do gateway ExpressRoute pode resolver esta questão. Desaentar a frequência com que a utilização do CPU excede um determinado limiar.

:::image type="content" source="./media/expressroute-monitoring-metrics-alerts/cpu-split.jpg" alt-text="Screenshot da utilização do CPU - métricas divididas.":::

### <a name="packets-per-second---split-by-instance"></a>Pacotes por segundo - Dividido por Exemplo

Tipo de agregação: *Avg*

Esta métrica captura o número de pacotes de entrada que atravessam o gateway ExpressRoute. Deverá ver um fluxo consistente de dados aqui se o seu portal estiver a receber tráfego da sua rede no local. Desaça um alerta para quando o número de pacotes por segundo desce abaixo de um limiar indicando que o seu gateway já não está a receber tráfego.

:::image type="content" source="./media/expressroute-monitoring-metrics-alerts/pps-split.jpg" alt-text="Screenshot de pacotes por segundo - métricas divididas.":::

### <a name="count-of-routes-advertised-to-peer---split-by-instance"></a>Contagem de rotas anunciadas a pares - Dividida por Instância

Tipo de agregação: *Contagem*

Esta métrica é a contagem para o número de rotas que o gateway ExpressRoute está publicitando para o circuito. Os espaços de endereço podem incluir redes virtuais que estão ligadas usando o espreitar VNet e utilizam o gateway ExpressRoute remoto. Deve esperar que o número de rotas permaneça consistente a menos que existam alterações frequentes nos espaços de endereço de rede virtual. Deite um alerta para quando o número de rotas anunciadas descer abaixo do limiar para o número de espaços de endereço de rede virtual que você conhece.

:::image type="content" source="./media/expressroute-monitoring-metrics-alerts/count-of-routes-advertised-to-peer.png" alt-text="Screenshot da contagem de rotas anunciadas a peer.":::

### <a name="count-of-routes-learned-from-peer---split-by-instance"></a>Contagem de Rotas Aprendidas a par - Dividida por Exemplo

Tipo de agregação: *Max*

Esta métrica mostra o número de rotas que o gateway ExpressRoute está a aprender com os pares ligados ao circuito ExpressRoute. Estas rotas podem ser de outra rede virtual ligada ao mesmo circuito ou aprendidas no local. Desateia um alerta para quando o número de rotas aprendidas descer abaixo de um determinado limiar. Isto pode indicar que ou o gateway está a ver um problema de desempenho ou os pares remotos já não são rotas publicitárias para o circuito ExpressRoute. 

:::image type="content" source="./media/expressroute-monitoring-metrics-alerts/count-of-routes-learned-from-peer.png" alt-text="Screenshot da contagem de rotas aprendidas pelos pares.":::

### <a name="frequency-of-routes-change---split-by-instance"></a>Variação da frequência das rotas - Divisão por Instância

Tipo de agregação: *Soma*

Esta métrica mostra a frequência das rotas aprendidas ou publicitadas a pares remotos. Primeiro, deve investigar os seus dispositivos no local para perceber porque é que a rede está a mudar com tanta frequência. Uma mudança de alta frequência nas rotas pode indicar um problema de desempenho no gateway ExpressRoute onde a escala do gateway SKU pode resolver o problema. Desaprote um alerta para um limiar de frequência para saber quando o seu gateway ExpressRoute está a ver alterações de rota anormais.

:::image type="content" source="./media/expressroute-monitoring-metrics-alerts/frequency-of-routes-changed.png" alt-text="A imagem da frequência das rotas mudou de métrica.":::

### <a name="number-of-vms-in-the-virtual-network"></a>Número de VMs na Rede Virtual

Tipo de agregação: *Max*

Esta métrica mostra o número de máquinas virtuais que estão a usar o gateway ExpressRoute. O número de máquinas virtuais pode incluir VMs de redes virtuais espreitadas que usam o mesmo gateway ExpressRoute. Desatenção para esta métrica se o número de VMs for superior a um determinado limiar que possa afetar o desempenho do gateway. 

:::image type="content" source="./media/expressroute-monitoring-metrics-alerts/number-of-virtual-machines-virtual-network.png" alt-text="Screenshot do número de máquinas virtuais na métrica da rede virtual.":::

## <a name="expressroute-gateway-connections-in-bitsseconds"></a>Ligações de gateway ExpressRoute em bits/segundos

Tipo de agregação: *Avg*

Esta métrica mostra o uso da largura de banda para uma ligação específica a um circuito ExpressRoute.

:::image type="content" source="./media/expressroute-monitoring-metrics-alerts/erconnections.jpg" alt-text="Screenshot da métrica de utilização da largura de banda de ligação gateway.":::

## <a name="alerts-for-expressroute-gateway-connections"></a>Alertas para ligações de gateway ExpressRoute

1. Para configurar alertas, navegue para **O Monitor Azure,** em seguida, selecione **Alertas**.

   :::image type="content" source="./media/expressroute-monitoring-metrics-alerts/eralertshowto.jpg" alt-text="alertas":::
2. **Selecione +Selecione Target** e selecione o recurso de ligação de gateway ExpressRoute.

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

## <a name="more-metrics-in-log-analytics"></a>Mais métricas em Log Analytics

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
