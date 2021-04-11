---
title: Monitorização da Azure Time Series Insights Referência de dados | Microsoft Docs
description: Documentação de referência para monitorização da Azure Time Series Insights.
author: deepakpalled
ms.author: lyhughes
manager: diviso
ms.workload: big-data
ms.service: time-series-insights
services: time-series-insights
ms.topic: conceptual
ms.date: 12/10/2020
ms.custom: lyrana
ms.openlocfilehash: bfd0d04313f0b519b4013a43e29d88400c73ea31
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/29/2021
ms.locfileid: "100591387"
---
# <a name="monitoring-azure-time-series-insights-data-reference"></a>Monitorização da referência de dados da Série De Tempo Azure Insights

Conheça os dados e recursos recolhidos pelo Azure Monitor a partir do seu ambiente Azure Time Series Insights. Consulte [os Insights das Séries de Tempo de Monitorização]( ./how-to-monitor-tsi.md) para obter informações sobre a recolha e análise de dados de monitorização.

## <a name="metrics"></a>Métricas

Esta secção lista todas as métricas da plataforma recolhidas automaticamente para o Azure Time Series Insights. Para obter uma lista de todas as métricas de suporte do Azure Monitor (incluindo insights da Série de Tempo Azure), consulte [as métricas suportadas pelo Azure Monitor](../azure-monitor/essentials/metrics-supported.md). O fornecedor de recursos para estas métricas é [o Microsoft.TimeSeriesInsights/environments/eventsources](../azure-monitor/essentials/metrics-supported.md#microsofttimeseriesinsightsenvironmentseventsources) e [Microsoft.TimeSeriesInsights/environments](../azure-monitor/essentials/metrics-supported.md#microsofttimeseriesinsightsenvironments).


### <a name="ingress"></a>Entrada
 
|Metric|Nome de exibição métrica|Unidade|Tipo de Agregação|Description|
|---|---|---|---|---|
|IngressReceivedBytes|Ingress Recebido Bytes|Bytes|Total|Contagem de bytes lidos a partir da fonte do evento|
|IngressReceivedInvalidMesages|Ingress recebeu mensagens inválidas|de palavras|Total|Contagem de mensagens inválidas lidas a partir da fonte do evento|
|IngressReceivedMessages|Mensagens Recebidas Ingress|de palavras|Total|Contagem de mensagens lidas a partir da fonte do evento|
|IngressReceivedMessagesCountLag|Ingress recebeu recado de mensagens|de palavras|Média|Diferença entre o número de sequência da última mensagem encosa na partição de origem do evento e o número de sequência de mensagens que estão a ser processadas em Ingress|
|IngressReceivedMesstimeLag|Ingress recebeu recado de tempo de mensagens|Segundos|Máximo|Diferença entre o tempo em que a mensagem é encosa na fonte do evento e o tempo que é processado em Ingress|
|IngressStoredBytes|Ingress Stored Bytes|Bytes|Total|Tamanho total dos eventos processados com sucesso e disponíveis para consulta|
|IngresssStoredEvents|Eventos Armazenados ingress|de palavras|Total|Contagem de eventos achatados processados com sucesso e disponíveis para consulta|

### <a name="storage"></a>Armazenamento

|Metric|Nome de exibição métrica|Unidade|Tipo de Agregação|Description|
|---|---|---|---|---|
|WarmStorageMaxProperties|Propriedades Max de Armazenamento Quente|de palavras|Máximo|Número máximo de propriedades utilizadas pelo ambiente para S1/S2 SKU e número máximo de propriedades permitidas pela Warm Store para PAYG SKU|
|WarmStorageUsedProperties|Propriedades usadas de armazenamento quente |de palavras|Máximo|Número de propriedades utilizadas pelo ambiente para S1/S2 SKU e número de propriedades utilizadas pela Warm Store para PAYG SKU|

## <a name="resource-logs"></a>Registos do recurso

Esta secção lista os tipos de registos de recursos que pode recolher para o seu ambiente Azure Time Series Insights.

| Categoria | Nome a Apresentar | Descrição |
|----- |----- |----- |
| Entrada | TSIIngress | A categoria Ingress rastreia erros que ocorrem no gasoduto de entrada. Esta categoria inclui erros que ocorrem ao receber eventos (tais como falhas na ligação a uma Fonte de Evento) e eventos de processamento (tais como erros ao analisar uma carga útil do evento). |

## <a name="schemas"></a>Esquemas
Os seguintes esquemas estão em uso pela Azure Time Series Insights

### <a name="tsiingress-table"></a>Tabela TSIIngress

| Propriedade | Descrição |
|----- |----- |
| TimeGenerated | Tempo (UTC) em que este evento é gerado. |
| Localização | A localização do recurso. |
| Categoria | Categoria do evento de registo. |
| OperationName | Nome da operação do evento. |
| CorrelationId | A identificação da correlação do pedido. |
| Level | O nível de gravidade do evento. |
| ResultDescription | Descrição do resultado da operação, como "Erro proibido recebido". |
| Mensagem | A mensagem associada ao erro. Inclui detalhes sobre o que correu mal e como mitigar o erro. |
| CódigoDoErro | O código associado ao erro |
| EventSourceType | O tipo de fonte de evento. Pode ser o Centro de Eventos ou o centro de IoT. |
| EventSourceProperties | Uma coleção de propriedades específicas da sua fonte de evento. Contém detalhes como o grupo de consumidores e o nome chave de acesso. |
