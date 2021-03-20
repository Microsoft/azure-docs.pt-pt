---
title: Monitoring Time Series Insights | Microsoft Docs
description: Monitor Time Series Insights para disponibilidade, desempenho e funcionamento.
author: deepakpalled
ms.author: lyhughes
manager: diviso
ms.workload: big-data
ms.service: time-series-insights
services: time-series-insights
ms.topic: conceptual
ms.date: 12/10/2020
ms.custom: lyrana
ms.openlocfilehash: a46ddeddfcefcd4d6e7f87747fe36cfc6ec82e35
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/20/2021
ms.locfileid: "101737567"
---
# <a name="monitoring-time-series-insights"></a>Insights das Séries de Tempo de Monitorização

Quando você tem aplicações críticas e processos de negócio contando com recursos Azure, você quer monitorizar esses recursos para sua disponibilidade, desempenho e funcionamento. Este artigo descreve os dados de monitorização gerados pela Time Series Insights e como pode utilizar as funcionalidades do Azure Monitor para analisar e alertar estes dados.

## <a name="monitor-overview"></a>Visão geral do monitor

A página **'Visão Geral'** no portal Azure para cada ambiente de Insights séries de tempo inclui uma breve visão da utilização do recurso, como o número de mensagens recebidas e o número de bytes armazenados. Estas informações são úteis, no entanto, apenas uma pequena quantidade dos dados de monitorização está disponível a partir deste painel. Alguns destes dados são recolhidos automaticamente e estão disponíveis para análise assim que criar o recurso. Pode ativar tipos adicionais de recolha de dados com alguma configuração.

## <a name="what-is-azure-monitor"></a>O que é Azure Monitor

Time Series Insights cria dados de monitorização usando [o Azure Monitor](../azure-monitor/overview.md), que é um serviço completo de monitorização de pilhas em Azure que fornece um conjunto completo de funcionalidades para monitorizar os seus recursos Azure, além de recursos em outras nuvens e no local.

Comece com o artigo [Monitorar recursos Azure com Azure Monitor,](../azure-monitor/essentials/monitor-azure-resource.md)que descreve os seguintes conceitos:

- O que é o Azure Monitor?
- Custos associados à monitorização
- Dados de monitorização recolhidos em Azure
- Recolha de dados configurado
- Ferramentas padrão em Azure para analisar e alertar sobre os dados de monitorização

As secções seguintes baseiam-se neste artigo descrevendo os dados específicos recolhidos para a Azure Time Series Insights. Estas secções também fornecem exemplos para configurar a recolha de dados e analisar estes dados com ferramentas Azure.

> [!TIP]
> Para compreender os custos associados ao Azure Monitor, consulte [a Utilização e os custos estimados.](../azure-monitor//usage-estimated-costs.md) Para compreender o tempo que os seus dados demoram a aparecer no Azure Monitor, consulte [o tempo de ingestão de dados do Registo](../azure-monitor/logs/data-ingestion-time.md).

## <a name="monitoring-data-from-azure-time-series-insights"></a>Dados de monitorização da Azure Time Series Insights

A Azure Time Series Insights recolhe os mesmos tipos de dados de monitorização que outros recursos Azure que são descritos na [monitorização de dados a partir de recursos Azure](../azure-monitor/essentials/monitor-azure-resource.md#monitoring-data). 

Consulte [a referência de dados da Série De Tempo Azure para](how-to-monitor-tsi-reference.md) obter uma referência detalhada dos registos e métricas que pode recolher.

## <a name="collection-and-routing"></a>Recolha e encaminhamento

As métricas da plataforma são recolhidas e armazenadas automaticamente, mas podem ser encaminhadas para outros locais utilizando uma definição de diagnóstico.

Os Registos de Recursos não são recolhidos e armazenados até criar uma definição de diagnóstico e encaminhá-los para um ou mais locais.
Consulte [Criar definição de diagnóstico para recolher registos e métricas da plataforma em Azure](../azure-monitor/essentials/diagnostic-settings.md) para o processo detalhado para criar uma definição de diagnóstico utilizando o portal Azure, CLI ou PowerShell. Quando cria uma definição de diagnóstico, especifica quais as categorias de registos a recolher.

Pode recolher registos das seguintes categorias para Azure Time Series Insights:

   | Categoria | Descrição |
   |---|---|
   | Entrada  | A categoria Ingress rastreia erros que ocorrem no gasoduto de entrada. Esta categoria inclui erros que ocorrem ao receber eventos (tais como falhas na ligação a uma Fonte de Evento) e eventos de processamento (tais como erros ao analisar uma carga útil do evento). |

## <a name="analyzing-metrics"></a>Análise de métricas

Pode analisar métricas para Azure Time Series Insights, juntamente com métricas de outros serviços Azure, abrindo métricas a partir do menu Azure Monitor. Consulte [o Azure Metrics Explorer](../azure-monitor/essentials/metrics-getting-started.md) para obter detalhes sobre a utilização desta ferramenta.

Para obter uma lista das métricas da plataforma recolhidas, consulte a referência de dados da [Série de Tempos Azure](how-to-monitor-tsi-reference.md#metrics)

Este exemplo mostra a contagem de bytes recebidos de todas as fontes de eventos no seu ambiente Azure Time Series Insights.

**Ingress recebeu bytes** [ ![ Azure Time Series ingressed bytes](media/how-to-monitor-tsi/ingress-received-bytes.png)](media/how-to-monitor-tsi/ingress-received-bytes.png#lightbox)

O exemplo mostra a contagem de bytes processados com sucesso e disponíveis para consulta no seu ambiente Azure Time Series Insights.

**Ingress armazenado bytes** [ ![ Azure Time Series ingress storeed bytes](media/how-to-monitor-tsi/ingress-stored-bytes.png)](media/how-to-monitor-tsi/ingress-stored-bytes.png#lightbox)

## <a name="analyzing-logs"></a>Análise de registos
Pode aceder a registos de recursos como uma bolha numa conta de armazenamento, como dados de eventos, ou através de consultas de Log Analytic.

Os dados em Registos monitores Azure são armazenados em tabelas que cada mesa tem o seu próprio conjunto de propriedades únicas.

Todos os registos de recursos no Azure Monitor têm os mesmos campos seguidos por campos específicos de serviço. O esquema comum é delineado no [esquema de registo de recursos do Azure Monitor](../azure-monitor/essentials/resource-logs-schema.md#top-level-common-schema). Para obter uma lista dos tipos de registos de recursos recolhidos para Azure Time Series Insights, consulte [a referência de dados da Série de Tempo Azure.](how-to-monitor-tsi-reference.md#resource-logs)

A Azure Time Series Insights armazena dados nas seguintes tabelas.

| Tabela | Descrição |
|:---|:---|
| TSIIngress | A tabela que armazena dados da categoria Ingress. A categoria Ingress rastreia erros que ocorrem no gasoduto de entrada. Esta categoria inclui erros que ocorrem ao receber eventos (tais como falhas na ligação a uma Fonte de Evento) e eventos de processamento (tais como erros ao analisar uma carga útil do evento).

Para encaminhar dados para Registos do Monitor Azure, tem de criar uma definição de diagnóstico para enviar registos de recursos ou métricas de plataforma para um espaço de trabalho do Log Analytics. Para saber mais, consulte [Coleção e encaminhamento.](../iot-hub/monitor-iot-hub.md#collection-and-routing)

## <a name="sample-queries"></a>Consultas de amostra

Seguem-se as consultas que pode utilizar para o ajudar a monitorizar o ambiente Azure Time Series Insights:

+ Obtenha detalhes sobre falhas de ligação de fonte de evento nos últimos cinco dias:

    ```Kusto
   TSIIngress
   | where OperationName == "Microsoft.TimeSeriesInsights/environments/eventsources/ingress/connect"
   | where _ResourceId contains "<your environment name, event source name, or the full event source resource URL>"
   | where TimeGenerated > ago(5d)

    ```
+ Obtenha detalhes sobre mensagens inválidas recebidas nos últimos cinco dias:

    ```Kusto
   TSIIngress
   | where OperationName == "Microsoft.TimeSeriesInsights/environments/eventsources/ingress/deserialize"
   | where _ResourceId contains "<your environment name, event source name, or the full event source resource URL>"
   | where TimeGenerated > ago(5d)

    ```

## <a name="alerts"></a>Alertas

Os alertas do Azure Monitor notificam-no proativamente quando forem encontradas condições importantes nos seus dados de monitorização. Permitem identificar e resolver problemas no seu sistema antes que os seus clientes os percebam. Pode definir alertas em [métricas,](../azure-monitor/alerts/alerts-metric-overview.md) [registos](../azure-monitor/alerts/alerts-unified-log.md)e no registo de [atividades](../azure-monitor/alerts/activity-log-alerts.md). Diferentes tipos de alertas têm benefícios e inconvenientes.

Ao criar uma regra de alerta baseada em métricas da plataforma, esteja ciente de que para as métricas da plataforma Time Series Insights que são recolhidas em unidades de contagem, algumas agregações podem não estar disponíveis ou utilizáveis.

## <a name="next-steps"></a>Passos Seguintes

* Consulte [a referência de dados da Série De Tempo Azure para](how-to-monitor-tsi-reference.md) uma referência dos registos e métricas criados pela Azure Time Series Insights.
* Consulte [os recursos de Monitor Azure com o Azure Monitor](../azure-monitor/essentials/monitor-azure-resource.md) para obter informações sobre a monitorização dos recursos do Azure.