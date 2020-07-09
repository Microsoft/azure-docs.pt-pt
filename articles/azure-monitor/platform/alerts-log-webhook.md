---
title: Ações webhook para alertas de registo em alertas Azure
description: Este artigo descreve como criar uma regra de alerta de registo utilizando o espaço de trabalho do Log Analytics ou o Application Insights, como o alerta empurra os dados como um webhook HTTP, e os detalhes das diferentes personalizações que são possíveis.
author: yanivlavi
ms.author: yalavi
services: monitoring
ms.topic: conceptual
ms.date: 06/25/2019
ms.subservice: alerts
ms.openlocfilehash: 6c9bacfc4354351cbbf2eb735414ff3334cd7d0a
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.contentlocale: pt-PT
ms.lasthandoff: 07/02/2020
ms.locfileid: "84323676"
---
# <a name="webhook-actions-for-log-alert-rules"></a>Ações webhook para regras de alerta de registo
Quando um [alerta de registo é criado em Azure,](alerts-log.md)tem a opção de o [configurar utilizando grupos](action-groups.md) de ação para executar uma ou mais ações. Este artigo descreve as diferentes ações do webhook que estão disponíveis e mostra como configurar um webhook personalizado baseado em JSON.

> [!NOTE]
> Também pode usar o [esquema de alerta comum](https://aka.ms/commonAlertSchemaDocs) para as suas integrações webhook. O esquema de alerta comum oferece a vantagem de ter uma única carga de alerta extensível e unificada em todos os serviços de alerta em Azure Monitor.Por favor, note que o esquema de alerta comum não honra a opção JSON personalizada para alertas de registo. Adia a carga útil do esquema de alerta comum se for selecionado independentemente da personalização que possa ter feito ao nível da regra de alerta. [Conheça as definições comuns de esquema de alerta.](https://aka.ms/commonAlertSchemaDefinitions)

## <a name="webhook-actions"></a>Ações do webhook

Com as ações webhook, pode invocar um processo externo através de um único pedido HTTP POST. O serviço chamado deve suportar webhooks e determinar como usar qualquer carga útil que receba.

As ações webhook requerem as propriedades na tabela seguinte.

| Propriedade | Descrição |
|:--- |:--- |
| **Webhook URL** |A URL do webhook. |
| **Carga personalizada JSON** |A carga personalizada para enviar com o webhook quando esta opção é escolhida durante a criação de alerta. Para obter mais informações, consulte [Gerir os alertas de registo .](alerts-log.md)|

> [!NOTE]
> O botão **Ver Webhook** ao lado da carga útil personalizada do JSON para a opção **webhook** para o alerta de registo exibe a carga útil do webhook da amostra para a personalização fornecida. Não contém dados reais, mas é representativo do esquema JSON que é usado para alertas de registo. 

Os Webhooks incluem um URL e uma carga útil formatada em JSON que os dados enviados para o serviço externo. Por predefinição, a carga útil inclui os valores na tabela seguinte. Pode optar por substituir esta carga útil por uma personalizada. Nesse caso, utilize as variáveis na tabela para cada um dos parâmetros para incluir os seus valores na sua carga personalizada.


| Parâmetro | Variável | Descrição |
|:--- |:--- |:--- |
| *Nome de AlertaRule* |#alertrulename |O nome da regra de alerta. |
| *Gravidade* |#severity |Severidade definida para o alerta de registo de disparo. |
| *AlertThresholdOperator* |#thresholdoperator |Operador de limiar para a regra de alerta, que utiliza maior ou menos do que. |
| *AlertaThresholdValue* |#thresholdvalue |Valor limiar para a regra de alerta. |
| *LinkToSearchResults* |#linktosearchresults |Link para o portal Analytics que devolve os registos da consulta que criou o alerta. |
| *LinkToSearchResultsAPI* |#linktosearchresultsapi |Link para a API Analytics que devolve os registos da consulta que criou o alerta. |
| *LinkToFilteredSearchResultsUI* |#linktofilteredsearchresultsui |Link para o portal Analytics que devolve os registos da consulta filtrada por dimensões combinações de valor que criaram o alerta. |
| *LinkToFilteredSearchResultsAPI* |#linktofilteredsearchresultsapi |Link para a API Analytics que devolve os registos da consulta filtrada por dimensões combinações de valor que criaram o alerta. |
| *ResultadosContam* |#searchresultcount |Número de registos nos resultados da pesquisa. |
| *Tempo de fim do intervalo de pesquisa* |#searchintervalendtimeutc |Fim do tempo para a consulta em UTC, com o formato mm/dd/yyyy HH:mm:ss AM/PM. |
| *Intervalo de pesquisa* |#searchinterval |Janela de tempo para a regra de alerta, com o formato HH:mm:ss. |
| *Intervalo de pesquisa Início de Tempo* |#searchintervalstarttimeutc |Hora de início para a consulta em UTC, com o formato mm/dd/yyyy HH:mm:ss AM/PM. 
| *SearchQuery* |#searchquery |Consulta de pesquisa de registo utilizada pela regra de alerta. |
| *SearchResults* |"IncludeSearchResults": verdadeiro|Os registos devolvidos pela consulta como uma tabela JSON, limitados aos primeiros 1.000 registos. "IncludeSearchResults": o verdadeiro é adicionado numa definição personalizada de webhook JSON como uma propriedade de alto nível. |
| *Dimensões* |"IncluirDimensionais": verdadeiro|As combinações de valor de dimensões que desencadearam o alerta como secção JSON. "IncludeDimensions": o verdadeiro é adicionado numa definição personalizada de webhook JSON como uma propriedade de alto nível. |
| *Tipo de alerta*| #alerttype | O tipo de regra de alerta de registo configurado como [medição métrica](alerts-unified-log.md#metric-measurement-alert-rules) ou [número de resultados](alerts-unified-log.md#number-of-results-alert-rules).|
| *WorkspaceID* |#workspaceid |ID do seu espaço de trabalho Log Analytics. |
| *ID da Aplicação* |#applicationid |ID da sua aplicação Application Insights. |
| *ID da subscrição* |#subscriptionid |ID da sua assinatura Azure usada. 

> [!NOTE]
> Os links fornecidos passam parâmetros como *SearchQuery*, *Search Interval StartTime*e *Search Interval End time* no URL para o portal Azure ou API.

Por exemplo, pode especificar a seguinte carga personalizada que inclui um único parâmetro chamado *texto*. O serviço que este webhook chama espera este parâmetro.

```json

    {
        "text":"#alertrulename fired with #searchresultcount over threshold of #thresholdvalue."
    }
```
Este exemplo de carga resolve algo como o seguinte quando é enviado para o webhook:

```json
    {
        "text":"My Alert Rule fired with 18 records over threshold of 10 ."
    }
```
Como todas as variáveis num webhook personalizado devem ser especificadas dentro de um recinto JSON, como "#searchinterval", o webhook resultante também tem dados variáveis dentro de recintos, como "00:05:00".

Para incluir os resultados da pesquisa numa carga útil personalizada, certifique-se de que **o IncludeSearchResults** é definido como uma propriedade de alto nível na carga útil JSON. 

## <a name="sample-payloads"></a>Cargas de amostra
Esta secção mostra cargas de amostra para webhooks para alertas de registo. As cargas da amostra incluem exemplos quando a carga útil é normal e quando é personalizada.

### <a name="standard-webhook-for-log-alerts"></a>Webhook padrão para alertas de registo 
Ambos os exemplos têm uma carga falsa com apenas duas colunas e duas linhas.

#### <a name="log-alert-for-log-analytics"></a>Alerta de registo para Log Analytics
A seguinte carga útil da amostra é para uma ação padrão webhook *sem uma opção JSON personalizada* que é usada para alertas baseados em Log Analytics:

```json
{
    "SubscriptionId": "12345a-1234b-123c-123d-12345678e",
    "AlertRuleName": "AcmeRule",
    "SearchQuery": "Perf | where ObjectName == \"Processor\" and CounterName == \"% Processor Time\" | summarize AggregatedValue = avg(CounterValue) by bin(TimeGenerated, 5m), Computer",
    "SearchIntervalStartTimeUtc": "2018-03-26T08:10:40Z",
    "SearchIntervalEndtimeUtc": "2018-03-26T09:10:40Z",
    "AlertThresholdOperator": "Greater Than",
    "AlertThresholdValue": 0,
    "ResultCount": 2,
    "SearchIntervalInSeconds": 3600,
    "LinkToSearchResults": "https://portal.azure.com/#Analyticsblade/search/index?_timeInterval.intervalEnd=2018-03-26T09%3a10%3a40.0000000Z&_timeInterval.intervalDuration=3600&q=Usage",
    "LinkToFilteredSearchResultsUI": "https://portal.azure.com/#Analyticsblade/search/index?_timeInterval.intervalEnd=2018-03-26T09%3a10%3a40.0000000Z&_timeInterval.intervalDuration=3600&q=Usage",
    "LinkToSearchResultsAPI": "https://api.loganalytics.io/v1/workspaces/workspaceID/query?query=Heartbeat&timespan=2020-05-07T18%3a11%3a51.0000000Z%2f2020-05-07T18%3a16%3a51.0000000Z",
    "LinkToFilteredSearchResultsAPI": "https://api.loganalytics.io/v1/workspaces/workspaceID/query?query=Heartbeat&timespan=2020-05-07T18%3a11%3a51.0000000Z%2f2020-05-07T18%3a16%3a51.0000000Z",
    "Description": "log alert rule",
    "Severity": "Warning",
    "AffectedConfigurationItems": [
        "INC-Gen2Alert"
    ],
    "Dimensions": [
        {
            "name": "Computer",
            "value": "INC-Gen2Alert"
        }
    ],
    "SearchResult": {
        "tables": [
            {
                "name": "PrimaryResult",
                "columns": [
                    {
                        "name": "$table",
                        "type": "string"
                    },
                    {
                        "name": "Computer",
                        "type": "string"
                    },
                    {
                        "name": "TimeGenerated",
                        "type": "datetime"
                    }
                ],
                "rows": [
                    [
                        "Fabrikam",
                        "33446677a",
                        "2018-02-02T15:03:12.18Z"
                    ],
                    [
                        "Contoso",
                        "33445566b",
                        "2018-02-02T15:16:53.932Z"
                    ]
                ]
            }
        ]
    },
    "WorkspaceId": "12345a-1234b-123c-123d-12345678e",
    "AlertType": "Metric measurement"
}
 ```

> [!NOTE]
> O valor de campo "Severidade" pode mudar se tiver mudado a sua preferência por [API](alerts-log-api-switch.md) para alertas de registo no Log Analytics.


#### <a name="log-alert-for-application-insights"></a>Alerta de registo para Insights de Aplicações
A seguinte carga útil da amostra é para um webhook padrão *sem uma opção JSON personalizada* quando é usado para alertas de registo com base em Insights de Aplicação:
    
```json
{
    "schemaId": "Microsoft.Insights/LogAlert",
    "data": {
        "SubscriptionId": "12345a-1234b-123c-123d-12345678e",
        "AlertRuleName": "AcmeRule",
        "SearchQuery": "requests | where resultCode == \"500\" | summarize AggregatedValue = Count by bin(Timestamp, 5m), IP",
        "SearchIntervalStartTimeUtc": "2018-03-26T08:10:40Z",
        "SearchIntervalEndtimeUtc": "2018-03-26T09:10:40Z",
        "AlertThresholdOperator": "Greater Than",
        "AlertThresholdValue": 0,
        "ResultCount": 2,
        "SearchIntervalInSeconds": 3600,
        "LinkToSearchResults": "https://portal.azure.com/AnalyticsBlade/subscriptions/12345a-1234b-123c-123d-12345678e/?query=search+*+&timeInterval.intervalEnd=2018-03-26T09%3a10%3a40.0000000Z&_timeInterval.intervalDuration=3600&q=Usage",
        "LinkToFilteredSearchResultsUI": "https://portal.azure.com/AnalyticsBlade/subscriptions/12345a-1234b-123c-123d-12345678e/?query=search+*+&timeInterval.intervalEnd=2018-03-26T09%3a10%3a40.0000000Z&_timeInterval.intervalDuration=3600&q=Usage",
        "LinkToSearchResultsAPI": "https://api.applicationinsights.io/v1/apps/0MyAppId0/metrics/requests/count",
        "LinkToFilteredSearchResultsAPI": "https://api.applicationinsights.io/v1/apps/0MyAppId0/metrics/requests/count",
        "Description": null,
        "Severity": "3",
        "Dimensions": [
            {
                "name": "IP",
                "value": "1.1.1.1"
            }
        ],
        "SearchResult": {
            "tables": [
                {
                    "name": "PrimaryResult",
                    "columns": [
                        {
                            "name": "$table",
                            "type": "string"
                        },
                        {
                            "name": "Id",
                            "type": "string"
                        },
                        {
                            "name": "Timestamp",
                            "type": "datetime"
                        }
                    ],
                    "rows": [
                        [
                            "Fabrikam",
                            "33446677a",
                            "2018-02-02T15:03:12.18Z"
                        ],
                        [
                            "Contoso",
                            "33445566b",
                            "2018-02-02T15:16:53.932Z"
                        ]
                    ]
                }
            ]
        },
        "ApplicationId": "123123f0-01d3-12ab-123f-abc1ab01c0a1",
        "AlertType": "Metric measurement"
    }
}
```

#### <a name="log-alert-with-custom-json-payload"></a>Alerta de registo com carga útil JSON personalizada
Por exemplo, para criar uma carga personalizada que inclua apenas o nome de alerta e os resultados da pesquisa, pode utilizar o seguinte: 

```json
    {
       "alertname":"#alertrulename",
       "IncludeSearchResults":true
    }
```

A seguinte carga útil da amostra destina-se a uma ação personalizada do webhook para qualquer alerta de registo:
    
```json
    {
    "alertname":"AcmeRule","IncludeSearchResults":true,
    "SearchResults":
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


## <a name="next-steps"></a>Próximos passos
- Saiba mais sobre [os alertas de registo em alertas Azure](alerts-unified-log.md).
- Entenda como [gerir os alertas de registo em Azure](alerts-log.md).
- Criar e gerir [grupos de ação em Azure.](action-groups.md)
- Saiba mais sobre [a Aplicação Insights](../../azure-monitor/app/analytics.md).
- Saiba mais sobre [consultas de registo.](../log-query/log-query-overview.md) 

