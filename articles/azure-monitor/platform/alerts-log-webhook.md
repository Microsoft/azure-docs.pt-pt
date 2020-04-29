---
title: Ações de webhook para alertas de log em alertas Azure
description: Este artigo descreve como criar uma regra de alerta de registo utilizando o espaço de trabalho de Log Analytics ou Os Insights de Aplicação, como o alerta empurra os dados como um webhook HTTP, e os detalhes das diferentes personalizações que são possíveis.
author: yanivlavi
ms.author: yalavi
services: monitoring
ms.topic: conceptual
ms.date: 06/25/2019
ms.subservice: alerts
ms.openlocfilehash: 7b1956ad2bf9bf38ba9edc4c7234078557564071
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/28/2020
ms.locfileid: "77667708"
---
# <a name="webhook-actions-for-log-alert-rules"></a>Ações de webhook para regras de alerta de log
Quando um alerta de [log é criado em Azure,](alerts-log.md)tem a opção de [configurá-lo utilizando grupos](action-groups.md) de ação para realizar uma ou mais ações. Este artigo descreve as diferentes ações de webhook que estão disponíveis e mostra como configurar um webhook personalizado baseado em JSON.

> [!NOTE]
> Também pode utilizar o esquema de [alerta comum](https://aka.ms/commonAlertSchemaDocs) para as suas integrações webhook. O esquema de alerta comum proporciona a vantagem de ter uma única carga de alerta extensível e unificada em todos os serviços de alerta no Monitor Azure.Por favor, note que o esquema de alerta comum não honra a opção personalizada jSON para alertas de registo. Adia para a carga útil do esquema de alerta comum se for selecionada independentemente da personalização que possa ter feito ao nível da regra de alerta. [Conheça as definições comuns de esquemade alerta.](https://aka.ms/commonAlertSchemaDefinitions)

## <a name="webhook-actions"></a>Ações do webhook

Com ações de webhook, pode invocar um processo externo através de um único pedido HTTP POST. O serviço que é chamado deve suportar webhooks e determinar como usar qualquer carga útil que receba.

As ações do Webhook requerem as propriedades na tabela seguinte.

| Propriedade | Descrição |
|:--- |:--- |
| **Webhook URL** |O URL do webhook. |
| **Carga útil JSON personalizada** |A carga útil personalizada para enviar com o webhook quando esta opção é escolhida durante a criação de alerta. Para mais informações, consulte [Gerir alertas](alerts-log.md)de registo .|

> [!NOTE]
> O botão **'Ver Webhook'** ao lado da **carga útil jSON personalizada para** a opção de webhook para o alerta de registo exibe a carga útil do webhook da amostra para a personalização fornecida. Não contém dados reais, mas é representativo do esquema JSON que é usado para alertas de registo. 

Os webhooks incluem um URL e uma carga de carga forrinada em JSON que os dados enviados para o serviço externo. Por predefinição, a carga útil inclui os valores na tabela seguinte. Pode optar por substituir esta carga por um personalizado. Nesse caso, utilize as variáveis na tabela para cada um dos parâmetros para incluir os seus valores na sua carga útil personalizada.


| Parâmetro | Variável | Descrição |
|:--- |:--- |:--- |
| *Nome de regras de alerta* |#alertrulename |Nome da regra do alerta. |
| *Gravidade* |#severity |Severidade definida para o alerta de registo disparado. |
| *AlertThresholdOperator* |#thresholdoperator |Operador de limiar para a regra de alerta, que utiliza maior ou inferior a. |
| *AlertThresholdValue* |#thresholdvalue |Valor limiar para a regra de alerta. |
| *Resultados de LinkToSearch* |#linktosearchresults |Link para o portal Analytics que devolve os registos da consulta que criou o alerta. |
| *ResultadoSContagem* |#searchresultcount |Número de registos nos resultados da pesquisa. |
| *Intervalo de pesquisa Tempo final* |#searchintervalendtimeutc |Tempo de fim para a consulta na UTC, com o formato mm/dd/yyyy HH:mm:ss AM/PM. |
| *Intervalo de pesquisa* |#searchinterval |Janela de tempo para a regra de alerta, com o formato HH:mm:ss. |
| *Hora de início do intervalo de pesquisa* |#searchintervalstarttimeutc |Hora de início da consulta na UTC, com o formato mm/dd/yyyy HH:mm:ss AM/PM. 
| *Pesquisação* |#searchquery |Consulta de pesquisa de registo utilizada pela regra do alerta. |
| *Resultados da pesquisa* |"Incluir Resultados de Pesquisa": verdadeiro|Registos devolvidos pela consulta como uma tabela JSON, limitado aos primeiros 1.000 registos, se "IncludeSearchResults": verdadeiro é adicionado numa definição de webhook jSON personalizada como uma propriedade de alto nível. |
| *Tipo de alerta*| #alerttype | O tipo de regra de alerta de registo configurado como [medição métrica](alerts-unified-log.md#metric-measurement-alert-rules) ou [número de resultados](alerts-unified-log.md#number-of-results-alert-rules).|
| *WorkspaceID* |#workspaceid |ID do seu espaço de trabalho Log Analytics. |
| *ID de aplicação* |#applicationid |ID da sua aplicação Application Insights. |
| *ID de subscrição* |#subscriptionid |Id da sua assinatura Azure utilizada. 

> [!NOTE]
> *LinkToSearchResults* passa por parâmetros como *SearchQuery,* *Search Interval StartTime*e *Search Interval Tempo final* no URL para o portal Azure para visualização na secção Analytics. O portal Azure tem um limite de tamanho URI de aproximadamente 2.000 caracteres. O portal *não* abrirá ligações fornecidas em alertas se os valores do parâmetro excederem o limite. Pode introduzir manualmente detalhes para visualizar resultados no portal Analytics. Ou pode utilizar a [API](https://dev.applicationinsights.io/documentation/Using-the-API) de Análise de Análise de Aplicações ou a [API DE REPOUSO](/rest/api/loganalytics/) DE Log Analytics para recuperar os resultados programáticamente. 

Por exemplo, pode especificar a seguinte carga personalizada que inclui um único parâmetro chamado *texto*. O serviço que este webhook chama espera este parâmetro.

```json

    {
        "text":"#alertrulename fired with #searchresultcount over threshold of #thresholdvalue."
    }
```
Esta carga útil de exemplo resolve-se em algo como o seguinte quando é enviado para o webhook:

```json
    {
        "text":"My Alert Rule fired with 18 records over threshold of 10 ."
    }
```
Como todas as variáveis num webhook personalizado devem ser especificadas dentro de um recinto JSON, como "#searchinterval", o webhook resultante também tem dados variáveis dentro dos recintos, como "00:05:00".

Para incluir os resultados da pesquisa numa carga útil personalizada, certifique-se de que **o IncludeSearchResults** é definido como uma propriedade de alto nível na carga útil JSON. 

## <a name="sample-payloads"></a>Cargas de amostra
Esta secção mostra cargas de amostra para webhooks para alertas de registo. As cargas da amostra incluem exemplos quando a carga é padrão e quando é personalizada.

### <a name="standard-webhook-for-log-alerts"></a>Webhook padrão para alertas de registo 
Ambos os exemplos têm uma carga de boneco com apenas duas colunas e duas linhas.

#### <a name="log-alert-for-log-analytics"></a>Alerta de log para Log Analytics
A seguinte carga útil da amostra é para uma ação padrão de webhook *sem uma opção JSON personalizada* que é usada para alertas baseados em Log Analytics:

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
> O valor do campo "Severity" pode mudar se tiver mudado a sua preferência de [API](alerts-log-api-switch.md) para alertas de registo no Log Analytics.


#### <a name="log-alert-for-application-insights"></a>Alerta de log para Insights de Aplicação
A seguinte carga útil da amostra é para um webhook padrão *sem uma opção JSON personalizada* quando é usado para alertas de log com base em Insights de Aplicação:
    
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

#### <a name="log-alert-with-custom-json-payload"></a>Alerta de log com carga útil json personalizada
Por exemplo, para criar uma carga personalizada que inclua apenas o nome de alerta e os resultados da pesquisa, pode utilizar o seguinte: 

```json
    {
       "alertname":"#alertrulename",
       "IncludeSearchResults":true
    }
```

A seguinte carga útil da amostra destina-se a uma ação personalizada de webhook para qualquer alerta de registo:
    
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
- Saiba mais sobre alertas de [log em alertas Azure](alerts-unified-log.md).
- Entenda como gerir alertas de [registo em Azure](alerts-log.md).
- Crie e gerea [grupos de ação em Azure.](action-groups.md)
- Saiba mais sobre [os Insights de Aplicação.](../../azure-monitor/app/analytics.md)
- Saiba mais sobre consultas de [registo.](../log-query/log-query-overview.md) 

