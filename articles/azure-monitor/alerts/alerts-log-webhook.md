---
title: Ações webhook para alertas de registo em alertas Azure
description: Descreve como configurar um alerta de log empurra com ação webhook e personalizações disponíveis
author: yanivlavi
ms.author: yalavi
services: monitoring
ms.topic: conceptual
ms.date: 09/22/2020
ms.openlocfilehash: 228193066c45421c4dddee1802aba1feed59e9c8
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/20/2021
ms.locfileid: "102042679"
---
# <a name="webhook-actions-for-log-alert-rules"></a>Ações de webhook para regras de alerta de registo

[O alerta de](alerts-log.md) registo suporta [a configuração de grupos de ação webhook](./action-groups.md#webhook). Neste artigo, descreveremos quais as propriedades disponíveis e como configurar um webhook JSON personalizado.

> [!NOTE]
> Webhook baseado em JSON personalizado não é atualmente suportado na versão API `2020-05-01-preview`

> [!NOTE]
> Recomenda-se que utilize [esquemas de alerta comuns](../alerts/alerts-common-schema.md) para as suas integrações webhook. O esquema comum de alerta proporciona a vantagem de ter uma única carga de alerta extensível e unificada em todos os serviços de alerta do Azure Monitor. Para regras de alerta de registo que tenham uma carga útil JSON personalizada definida, permitir que o esquema comum reverta o esquema de carga útil para o [descrito aqui](../alerts/alerts-common-schema-definitions.md#log-alerts). Os alertas com o esquema comum ativado têm um limite de tamanho superior de 256 KB por alerta, alerta maior não incluirá resultados de pesquisa. Quando os resultados da pesquisa não estiverem incluídos, deverá utilizar os `LinkToFilteredSearchResultsAPI` resultados de consulta ou `LinkToSearchResultsAPI` para aceder aos resultados da consulta através da API do Log Analytics.

## <a name="webhook-payload-properties"></a>Propriedades de carga útil Webhook

As ações do Webhook permitem-lhe invocar um único pedido HTTP POST. O serviço chamado deve suportar webhooks e saber como usar a carga útil que recebe.

Propriedades de ação webhook padrão e os seus nomes de parâmetros JSON personalizados:

| Parâmetro | Variável | Descrição |
|:--- |:--- |:--- |
| *Nome de AlertaRule* |#alertrulename |O nome da regra de alerta. |
| *Gravidade* |#severity |Severidade definida para o alerta de registo de disparo. |
| *AlertThresholdOperator* |#thresholdoperator |Operador de limiar para a regra de alerta. |
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
| *Tipo de alerta*| #alerttype | O tipo de regra de alerta de registo configurado como [medição métrica ou número de resultados](./alerts-unified-log.md#measure).|
| *WorkspaceID* |#workspaceid |ID do seu espaço de trabalho Log Analytics. |
| *ID da Aplicação* |#applicationid |ID da sua aplicação Application Insights. |
| *ID da Subscrição* |#subscriptionid |ID da sua assinatura Azure usada. |

## <a name="custom-webhook-payload-definition"></a>Definição de carga útil personalizada webhook

Pode utilizar a **carga útil personalizada do JSON para** obter uma carga personalizada do JSON utilizando os parâmetros acima. Também pode gerar propriedades adicionais.
Por exemplo, pode especificar a seguinte carga personalizada que inclui um único parâmetro chamado *texto*. O serviço que este webhook chama espera este parâmetro:

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
As variáveis num webhook personalizado devem ser especificadas dentro de um recinto JSON. Por exemplo, a referência a "#searchresultcount" no exemplo do webhook acima irá ser transmitida com base nos resultados do alerta.

Para incluir os resultados da pesquisa, adicione **IncluiSearchResults** como uma propriedade de alto nível no JSON personalizado. Os resultados da pesquisa são incluídos como uma estrutura JSON, por isso os resultados não podem ser referenciados em campos definidos personalizados. 

> [!NOTE]
> O botão **Ver Webhook** ao lado da carga útil JSON personalizada para visualização da opção **webhook** exibe pré-visualização do que foi fornecido. Não contém dados reais, mas é representativo do esquema JSON que será usado. 

## <a name="sample-payloads"></a>Cargas de amostra
Esta secção mostra cargas de amostra para webhooks para alertas de registo. As cargas da amostra incluem exemplos quando a carga útil é normal e quando é personalizada.

### <a name="log-alert-for-log-analytics"></a>Alerta de registo para Log Analytics
A seguinte carga útil da amostra é para uma ação padrão webhook que é usada para alertas baseados em Log Analytics:

> [!NOTE]
> O valor de campo "Severidade" muda se [tiver mudado para a API de Regras De Registo atuais](../alerts/alerts-log-api-switch.md) do legado Log Analytics Alert [API](./api-alerts.md).

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

### <a name="log-alert-for-application-insights"></a>Alerta de registo para Insights de Aplicações
A seguinte carga útil da amostra é para um webhook padrão quando é usado para alertas de registo com base em recursos de Insights de Aplicação:
    
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

### <a name="log-alert-for-other-resources-logs-from-api-version-2020-05-01-preview"></a>Alerta de registo de outros registos de recursos (a partir da versão `2020-05-01-preview` API)

> [!NOTE]
> Atualmente, não existem taxas adicionais para a versão API `2020-05-01-preview` e alertas de registo centrados em recursos.  Os preços das funcionalidades que estão em pré-visualização serão anunciados no futuro e um aviso fornecido antes do início da faturação. Caso opte por continuar a utilizar novas versões API e alertas de registo centrados em recursos após o período de pré-aviso, será faturado à taxa aplicável.

A seguinte carga útil da amostra é para um webhook padrão quando é usado para alertas de registo com base em outros registos de recursos (excluindo espaços de trabalho e Insights de Aplicação):

```json
{
    "schemaId": "azureMonitorCommonAlertSchema",
    "data": {
        "essentials": {
            "alertId": "/subscriptions/12345a-1234b-123c-123d-12345678e/providers/Microsoft.AlertsManagement/alerts/12345a-1234b-123c-123d-12345678e",
            "alertRule": "AcmeRule",
            "severity": "Sev4",
            "signalType": "Log",
            "monitorCondition": "Fired",
            "monitoringService": "Log Alerts V2",
            "alertTargetIDs": [
                "/subscriptions/12345a-1234b-123c-123d-12345678e/resourcegroups/ai-engineering/providers/microsoft.compute/virtualmachines/testvm"
            ],
            "originAlertId": "123c123d-1a23-1bf3-ba1d-dd1234ff5a67",
            "firedDateTime": "2020-07-09T14:04:49.99645Z",
            "description": "log alert rule V2",
            "essentialsVersion": "1.0",
            "alertContextVersion": "1.0"
        },
        "alertContext": {
            "properties": null,
            "conditionType": "LogQueryCriteria",
            "condition": {
                "windowSize": "PT10M",
                "allOf": [
                    {
                        "searchQuery": "Heartbeat",
                        "metricMeasure": null,
                        "targetResourceTypes": "['Microsoft.Compute/virtualMachines']",
                        "operator": "LowerThan",
                        "threshold": "1",
                        "timeAggregation": "Count",
                        "dimensions": [
                            {
                                "name": "ResourceId",
                                "value": "/subscriptions/12345a-1234b-123c-123d-12345678e/resourceGroups/TEST/providers/Microsoft.Compute/virtualMachines/testvm"
                            }
                        ],
                        "metricValue": 0.0,
                        "failingPeriods": {
                            "numberOfEvaluationPeriods": 1,
                            "minFailingPeriodsToAlert": 1
                        },
                        "linkToSearchResultsUI": "https://portal.azure.com#@12f345bf-12f3-12af-12ab-1d2cd345db67/blade/Microsoft_Azure_Monitoring_Logs/LogsBlade/source/Alerts.EmailLinks/scope/%7B%22resources%22%3A%5B%7B%22resourceId%22%3A%22%2Fsubscriptions%2F12345a-1234b-123c-123d-12345678e%2FresourceGroups%2FTEST%2Fproviders%2FMicrosoft.Compute%2FvirtualMachines%2Ftestvm%22%7D%5D%7D/q/eJzzSE0sKklKTSypUSjPSC1KVQjJzE11T81LLUosSU1RSEotKU9NzdNIAfJKgDIaRgZGBroG5roGliGGxlYmJlbGJnoGEKCpp4dDmSmKMk0A/prettify/1/timespan/2020-07-07T13%3a54%3a34.0000000Z%2f2020-07-09T13%3a54%3a34.0000000Z",
                        "linkToFilteredSearchResultsUI": "https://portal.azure.com#@12f345bf-12f3-12af-12ab-1d2cd345db67/blade/Microsoft_Azure_Monitoring_Logs/LogsBlade/source/Alerts.EmailLinks/scope/%7B%22resources%22%3A%5B%7B%22resourceId%22%3A%22%2Fsubscriptions%2F12345a-1234b-123c-123d-12345678e%2FresourceGroups%2FTEST%2Fproviders%2FMicrosoft.Compute%2FvirtualMachines%2Ftestvm%22%7D%5D%7D/q/eJzzSE0sKklKTSypUSjPSC1KVQjJzE11T81LLUosSU1RSEotKU9NzdNIAfJKgDIaRgZGBroG5roGliGGxlYmJlbGJnoGEKCpp4dDmSmKMk0A/prettify/1/timespan/2020-07-07T13%3a54%3a34.0000000Z%2f2020-07-09T13%3a54%3a34.0000000Z",
                        "linkToSearchResultsAPI": "https://api.loganalytics.io/v1/subscriptions/12345a-1234b-123c-123d-12345678e/resourceGroups/TEST/providers/Microsoft.Compute/virtualMachines/testvm/query?query=Heartbeat%7C%20where%20TimeGenerated%20between%28datetime%282020-07-09T13%3A44%3A34.0000000%29..datetime%282020-07-09T13%3A54%3A34.0000000%29%29&timespan=2020-07-07T13%3a54%3a34.0000000Z%2f2020-07-09T13%3a54%3a34.0000000Z",
                        "linkToFilteredSearchResultsAPI": "https://api.loganalytics.io/v1/subscriptions/12345a-1234b-123c-123d-12345678e/resourceGroups/TEST/providers/Microsoft.Compute/virtualMachines/testvm/query?query=Heartbeat%7C%20where%20TimeGenerated%20between%28datetime%282020-07-09T13%3A44%3A34.0000000%29..datetime%282020-07-09T13%3A54%3A34.0000000%29%29&timespan=2020-07-07T13%3a54%3a34.0000000Z%2f2020-07-09T13%3a54%3a34.0000000Z"
                    }
                ],
                "windowStartTime": "2020-07-07T13:54:34Z",
                "windowEndTime": "2020-07-09T13:54:34Z"
            }
        }
    }
}
```

### <a name="log-alert-with-a-custom-json-payload"></a>Alerta de registo com uma carga personalizada JSON
Por exemplo, para criar uma carga personalizada que inclua apenas o nome de alerta e os resultados da pesquisa, utilize esta configuração: 

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

## <a name="next-steps"></a>Passos seguintes
- Saiba mais sobre [os alertas de registo em alertas Azure](./alerts-unified-log.md).
- Entenda como [gerir os alertas de registo em Azure](alerts-log.md).
- Criar e gerir [grupos de ação em Azure.](./action-groups.md)
- Saiba mais sobre [a Aplicação Insights](../logs/log-query-overview.md).
- Saiba mais sobre [consultas de registo.](../logs/log-query-overview.md)