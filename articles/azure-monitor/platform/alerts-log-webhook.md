---
title: Ações de Webhook para alertas de registo nos alertas do Azure
description: Este artigo descreve como criar uma regra de alerta de registo utilizando a área de trabalho do Log Analytics ou o Application Insights, como o alerta envia dados como um webhook HTTP e os detalhes das personalizações diferentes que são possíveis.
author: msvijayn
services: monitoring
ms.service: azure-monitor
ms.topic: conceptual
ms.date: 06/25/2019
ms.author: vinagara
ms.subservice: alerts
ms.openlocfilehash: 6aa007c621e76cb0c188a7dab6279fd9e387b2b3
ms.sourcegitcommit: c105ccb7cfae6ee87f50f099a1c035623a2e239b
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 07/09/2019
ms.locfileid: "67705193"
---
# <a name="webhook-actions-for-log-alert-rules"></a>Ações de Webhook para regras de alerta de registo
Quando um [alerta de registo é criada no Azure](alerts-log.md), tem a opção de [configurá-lo ao utilizar grupos de ação](action-groups.md) para efetuar uma ou mais ações. Este artigo descreve as ações de webhook diferentes que estão disponíveis e mostra como configurar um webhook com base em JSON personalizado.

> [!NOTE]
> Também pode utilizar o [esquema comum do alerta](https://aka.ms/commonAlertSchemaDocs) para suas integrações do webhook. O esquema de alerta comum fornece a vantagem de ter um payload alerta único extensível e unificado em todos os serviços alerta no Azure Monitor. [Saiba mais sobre as definições de esquema de alerta comuns.](https://aka.ms/commonAlertSchemaDefinitions)

## <a name="webhook-actions"></a>Ações de Webhook

Com as ações de webhook, pode invocar um processo externo através de um único pedido de HTTP POST. O serviço de chamada deve suportar webhooks e determinar como utilizar o payload de que recebe.

Ações de Webhook exigem as propriedades na tabela seguinte.

| Propriedade | Descrição |
|:--- |:--- |
| **Webhook URL** |O URL do webhook. |
| **Payload JSON personalizado** |O payload personalizado para enviar com o webhook quando esta opção for escolhida durante a criação do alerta. Para obter mais informações, consulte [gerir alertas de registo](alerts-log.md).|

> [!NOTE]
> O **vista de Webhook** botão juntamente com o **incluir payload JSON personalizado para webhook** opção para o alerta de registo apresenta o payload do webook de exemplo para a personalização que foi fornecido. Ele não contém dados reais, mas representa o esquema JSON do que é utilizado para alertas de registo. 

Webhooks incluem um URL e uma carga formatado em JSON que os dados enviados para o serviço externo. Por predefinição, o payload inclui os valores na tabela seguinte. Pode optar por substituir este payload com um personalizado seus próprios. Nesse caso, utilize as variáveis na tabela para cada um dos parâmetros para incluir os respetivos valores no payload personalizado.


| Parâmetro | Variável | Descrição |
|:--- |:--- |:--- |
| *AlertRuleName* |#alertrulename |Nome da regra de alerta. |
| *Gravidade* |#severity |Gravidade definida para o alerta de registo de acionamento. |
| *AlertThresholdOperator* |#thresholdoperator |Operador de limiar para a regra de alerta, que usa o maior ou menor que. |
| *AlertThresholdValue* |#thresholdvalue |Valor de limiar para a regra de alerta. |
| *LinkToSearchResults* |#linktosearchresults |Ligar ao portal do Analytics que devolve os registos da consulta que criou o alerta. |
| *ResultCount* |#searchresultcount |Número de registos nos resultados da pesquisa. |
| *Hora de fim do intervalo de pesquisa* |#searchintervalendtimeutc |Terminar a tempo para a consulta em UTC, com o formato mm/dd/aaaa hh: mm: ss AM/PM. |
| *Intervalo de pesquisa* |#searchinterval |Janela de tempo para a regra de alerta, com o formato hh: mm:. |
| *StartTime de intervalo de pesquisa* |#searchintervalstarttimeutc |Hora de início para a consulta em UTC, com o formato mm/dd/aaaa hh: mm: ss AM/PM. 
| *SearchQuery* |#searchquery |Consulta de pesquisa de registo utilizada pela regra de alerta. |
| *SearchResults* |"IncludeSearchResults": true|Registos devolvidos pela consulta como uma tabela JSON, limitada para os primeiros 1000 registos, se "IncludeSearchResults": true é adicionado numa definição de webhook JSON personalizada como uma propriedade de nível superior. |
| *Tipo de alerta*| #alerttype | O tipo de regra de alerta de registo configurada como [medida da métrica](alerts-unified-log.md#metric-measurement-alert-rules) ou [número de resultados](alerts-unified-log.md#number-of-results-alert-rules).|
| *WorkspaceID* |#workspaceid |ID da sua área de trabalho do Log Analytics. |
| *ID da aplicação* |#applicationid |ID do seu Application Insights aplicação. |
| *ID de subscrição* |#subscriptionid |ID da sua subscrição do Azure utilizada. 

> [!NOTE]
> *LinkToSearchResults* passa parâmetros como *SearchQuery*, *StartTime de intervalo de pesquisa*, e *hora de fim do intervalo de pesquisa* no URL para o Azure Portal para visualização na secção de análise. O portal do Azure tem um limite de tamanho URI de aproximadamente 2000 carateres. O portal irá *não* abrir ligações fornecidas nos alertas, se os valores de parâmetro excederem o limite. Pode introduzir manualmente os detalhes para ver os resultados no portal do Analytics. Em alternativa, pode utilizar o [API do REST de análise do Application Insights](https://dev.applicationinsights.io/documentation/Using-the-API) ou o [API de REST do Log Analytics](/rest/api/loganalytics/) para obter os resultados por meio de programação. 

Por exemplo, pode especificar o seguinte o payload personalizado que inclui um único parâmetro chamado *texto*. O serviço que chama este webhook espera este parâmetro.

```json

    {
        "text":"#alertrulename fired with #searchresultcount over threshold of #thresholdvalue."
    }
```
Quando são enviado para o webhook para resolver este payload de exemplo para algo semelhante ao seguinte:

```json
    {
        "text":"My Alert Rule fired with 18 records over threshold of 10 ."
    }
```
Uma vez que todas as variáveis num webhook personalizado tem de ser especificadas dentro de um bastidor JSON, como "#searchinterval", o webhook resultante também tem dados da variável dentro de inclusões, como "00: 05:00."

Para incluir os resultados da pesquisa num payload personalizado, certifique-se de que **IncludeSearchResults** está definido como uma propriedade de nível superior no payload de JSON. 

## <a name="sample-payloads"></a>Cargas de exemplo
Esta secção mostra payloads de exemplo para webhooks para alertas de registo. As cargas de exemplo incluem exemplos de quando a carga é padrão e quando é personalizada.

### <a name="standard-webhook-for-log-alerts"></a>Padrão webhook para alertas de registo 
Ambos estes exemplos tem um payload fictício com apenas duas colunas e linhas de duas.

#### <a name="log-alert-for-log-analytics"></a>Alerta de registo para o Log Analytics
O payload de exemplo seguinte é para uma ação de webhook padrão *sem uma opção de JSON personalizada* que é utilizado para os alertas com base no Log Analytics:

```json
{
    "SubscriptionId":"12345a-1234b-123c-123d-12345678e",
    "AlertRuleName":"AcmeRule",
    "SearchQuery":"Perf | where ObjectName == \"Processor\" and CounterName == \"% Processor Time\" | summarize AggregatedValue = avg(CounterValue) by bin(TimeGenerated, 5m), Computer",
    "SearchIntervalStartTimeUtc": "2018-03-26T08:10:40Z",
    "SearchIntervalEndtimeUtc": "2018-03-26T09:10:40Z",
    "AlertThresholdOperator": "Greater Than",
    "AlertThresholdValue": 0,
    "ResultCount": 2,
    "SearchIntervalInSeconds": 3600,
    "LinkToSearchResults": "https://portal.azure.com/#Analyticsblade/search/index?_timeInterval.intervalEnd=2018-03-26T09%3a10%3a40.0000000Z&_timeInterval.intervalDuration=3600&q=Usage",
    "Description": "log alert rule",
    "Severity": "Warning",
    "SearchResult":
        {
        "tables":[
                    {"name":"PrimaryResult","columns":
                        [
                        {"name":"$table","type":"string"},
                        {"name":"Id","type":"string"},
                        {"name":"TimeGenerated","type":"datetime"}
                        ],
                    "rows":
                        [
                            ["Fabrikam","33446677a","2018-02-02T15:03:12.18Z"],
                            ["Contoso","33445566b","2018-02-02T15:16:53.932Z"]
                        ]
                    }
                ]
        },
    "WorkspaceId":"12345a-1234b-123c-123d-12345678e",
    "AlertType": "Metric measurement"
 }
 ```

> [!NOTE]
> O valor do campo "Severity" poderá ser alterado se tiver [mudado a sua preferência de API](alerts-log-api-switch.md) para alertas de registos no Log Analytics.


#### <a name="log-alert-for-application-insights"></a>Alerta de registo para o Application Insights
O payload de exemplo seguinte é para um webhook padrão *sem uma opção de JSON personalizada* quando ela é usada para alertas de registo com base no Application Insights:
    
```json
{
    "schemaId":"Microsoft.Insights/LogAlert","data":
    { 
    "SubscriptionId":"12345a-1234b-123c-123d-12345678e",
    "AlertRuleName":"AcmeRule",
    "SearchQuery":"requests | where resultCode == \"500\"",
    "SearchIntervalStartTimeUtc": "2018-03-26T08:10:40Z",
    "SearchIntervalEndtimeUtc": "2018-03-26T09:10:40Z",
    "AlertThresholdOperator": "Greater Than",
    "AlertThresholdValue": 0,
    "ResultCount": 2,
    "SearchIntervalInSeconds": 3600,
    "LinkToSearchResults": "https://portal.azure.com/AnalyticsBlade/subscriptions/12345a-1234b-123c-123d-12345678e/?query=search+*+&timeInterval.intervalEnd=2018-03-26T09%3a10%3a40.0000000Z&_timeInterval.intervalDuration=3600&q=Usage",
    "Description": null,
    "Severity": "3",
    "SearchResult":
        {
        "tables":[
                    {"name":"PrimaryResult","columns":
                        [
                        {"name":"$table","type":"string"},
                        {"name":"Id","type":"string"},
                        {"name":"TimeGenerated","type":"datetime"}
                        ],
                    "rows":
                        [
                            ["Fabrikam","33446677a","2018-02-02T15:03:12.18Z"],
                            ["Contoso","33445566b","2018-02-02T15:16:53.932Z"]
                        ]
                    }
                ]
        },
    "ApplicationId": "123123f0-01d3-12ab-123f-abc1ab01c0a1",
    "AlertType": "Number of results"
    }
}
```

#### <a name="log-alert-with-custom-json-payload"></a>Alerta de registo com o payload JSON personalizado
Por exemplo, para criar um payload personalizado que inclua apenas o nome do alerta e os resultados da pesquisa, pode utilizar o seguinte: 

```json
    {
       "alertname":"#alertrulename",
       "IncludeSearchResults":true
    }
```

O payload de exemplo seguinte é para uma ação personalizada de webhook para qualquer alerta de registo:
    
```json
    {
    "alertname":"AcmeRule","IncludeSearchResults":true,
    "SearchResult":
        {
        "tables":[
                    {"name":"PrimaryResult","columns":
                        [
                        {"name":"$table","type":"string"},
                        {"name":"Id","type":"string"},
                        {"name":"TimeGenerated","type":"datetime"}
                        ],
                    "rows":
                        [
                            ["Fabrikam","33446677a","2018-02-02T15:03:12.18Z"],
                            ["Contoso","33445566b","2018-02-02T15:16:53.932Z"]
                        ]
                    }
                ]
        }
    }
```


## <a name="next-steps"></a>Passos Seguintes
- Saiba mais sobre [alertas de registo nos alertas do Azure](alerts-unified-log.md).
- Compreender como [gerir alertas de registo no Azure](alerts-log.md).
- Criar e gerir [grupos de ação no Azure](action-groups.md).
- Saiba mais sobre [Application Insights](../../azure-monitor/app/analytics.md).
- Saiba mais sobre [registar as consultas](../log-query/log-query-overview.md). 

