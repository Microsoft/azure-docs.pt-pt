---
title: Ações de Webhook para alertas de registo nos alertas do Azure
description: Este artigo descreve como para uma regra de alerta de registo com o log analytics ou aplicativo insights, irá enviar dados por push como webhook de HTTP e os detalhes das personalizações diferentes possíveis.
author: msvijayn
services: monitoring
ms.service: azure-monitor
ms.topic: conceptual
ms.date: 05/01/2018
ms.author: vinagara
ms.subservice: alerts
ms.openlocfilehash: 908422927feabd156c5dcdc7a04d44ff8fc42094
ms.sourcegitcommit: 7e772d8802f1bc9b5eb20860ae2df96d31908a32
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/06/2019
ms.locfileid: "57442890"
---
# <a name="webhook-actions-for-log-alert-rules"></a>Ações de Webhook para regras de alerta de registo
Quando um [alerta de registo é criada no Azure](alerts-log.md), tem a opção de [configurar a utilização de grupos de ação](action-groups.md) para efetuar uma ou mais ações.  Este artigo descreve as ações de webhook diferentes que estão disponíveis e os detalhes sobre como configurar o webhook com base em JSON personalizado.


## <a name="webhook-actions"></a>Ações de Webhook

Ações de Webhook permitem-lhe invocar um processo externo através de um único pedido de HTTP POST.  O serviço que está a ser chamado deve suportar webhooks e determinar como ele usa qualquer carga que recebe.    

Ações de Webhook exigem as propriedades na tabela a seguir:

| Propriedade | Descrição |
|:--- |:--- |
| URL de webhook |O URL do webhook. |
| Payload JSON personalizado |Payload personalizado para enviar com o webhook, quando esta opção for escolhida durante a criação do alerta. Detalhes disponíveis em [gerir alertas de registo](alerts-log.md) |

> [!NOTE]
> Botão de Webhook do Vista juntamente com *incluir payload JSON personalizado para webhook* opção para o registo de alerta, irá apresentar o payload do webook de exemplo para a personalização fornecida. Não contém dados reais e representativo do esquema JSON utilizado para os alertas de registo. 

Webhooks incluem um URL e uma carga formatado em JSON que é os dados enviados para o serviço externo.  Por predefinição, o payload inclui os valores na tabela a seguir:  Pode optar por substituir este payload com um personalizado seus próprios.  Nesse caso pode utilizar as variáveis na tabela para cada um dos parâmetros para incluir o seu valor no payload personalizado.


| Parâmetro | Variável | Descrição |
|:--- |:--- |:--- |
| AlertRuleName |#alertrulename |Nome da regra de alerta. |
| Gravidade |#severity |Gravidade definida para o alerta de registo de acionamento. |
| AlertThresholdOperator |#thresholdoperator |Operador de limiar para a regra de alerta.  *Maior* ou *inferior a*. |
| AlertThresholdValue |#thresholdvalue |Valor de limiar para a regra de alerta. |
| LinkToSearchResults |#linktosearchresults |Ligar ao portal de análise que devolve os registos da consulta que criou o alerta. |
| ResultCount |#searchresultcount |Número de registos nos resultados da pesquisa. |
| Hora de fim do intervalo de pesquisa |#searchintervalendtimeutc |Hora para a consulta em UTC, fim format - mm/dd/aaaa hh: mm: ss AM/PM. |
| Intervalo de pesquisa |#searchinterval |Regra de janela de tempo para o alerta, formato - hh: mm:. |
| StartTime de intervalo de pesquisa |#searchintervalstarttimeutc |Hora de início para a consulta em UTC, format - mm/dd/aaaa hh: mm: ss AM/PM... 
| SearchQuery |#searchquery |Consulta de pesquisa de registo utilizada pela regra de alerta. |
| SearchResults |"IncludeSearchResults": true|Registos devolvidos pela consulta como uma tabela de JSON, limitada para os primeiros 1000 registos; Se "IncludeSearchResults": true é adicionado na definição de webhook JSON personalizada como uma propriedade de nível superior. |
| WorkspaceID |#workspaceid |ID da sua área de trabalho do Log Analytics. |
| ID da aplicação |#applicationid |ID do seu Application Insight aplicação. |
| ID da subscrição |#subscriptionid |ID da sua subscrição do Azure utilizado com o Application Insights. 

> [!NOTE]
> LinkToSearchResults passa parâmetros como o tempo de SearchQuery, StartTime de intervalo de pesquisa e final do intervalo de pesquisa no URL do portal do Azure para visualização na secção de análise. Portal do Azure tem URI limite de cerca de 2000 caracteres do tamanho e serão *não* abrir a ligação fornecida nos alertas, se os valores de parâmetros excederem o limite de disse. Os utilizadores podem introduzir manualmente detalhes para ver os resultados no portal de análise ou utilizar o [API do REST de análise do Application Insights](https://dev.applicationinsights.io/documentation/Using-the-API) ou [API de REST do Log Analytics](https://dev.loganalytics.io/reference) para obter os resultados por meio de programação 

Por exemplo, pode especificar o seguinte o payload personalizado que inclui um único parâmetro chamado *texto*.  O serviço que chama este webhook seria de esperando que este parâmetro.

```json

    {
        "text":"#alertrulename fired with #searchresultcount over threshold of #thresholdvalue."
    }
```
Este payload de exemplo deve resolver para algo semelhante ao seguinte quando enviados para o webhook.

```json
    {
        "text":"My Alert Rule fired with 18 records over threshold of 10 ."
    }
```
Que tenham todas as variáveis num webhook personalizado especificado no bastidor JSON, como "#searchinterval", o webhook resultante também terão os dados da variável dentro de bastidor, como "00: 05:00".

Para incluir os resultados da pesquisa num payload personalizado, certifique-se de que **IncludeSearchResults** está definido como uma propriedade de nível superior no payload de json. 

## <a name="sample-payloads"></a>Cargas de exemplo
Esta secção mostra o payload de exemplo do webhook para alertas de registo, incluindo a carga é padrão e quando seu custom.

### <a name="standard-webhook-for-log-alerts"></a>Padrão Webhook para alertas de registo 
Ambos estes exemplos tiveram indicado um payload fictício com apenas duas colunas e linhas de duas.

#### <a name="log-alert-for-azure-log-analytics"></a>Alerta de registo para o Log-Analytics do Azure
Segue-se um payload de exemplo para uma ação padrão de webhook *sem opção de Json personalizada* a ser utilizado para alertas com base na análise do registo.

```json
{
    "WorkspaceId":"12345a-1234b-123c-123d-12345678e",
    "AlertRuleName":"AcmeRule","SearchQuery":"search *",
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
    "SearchIntervalStartTimeUtc": "2018-03-26T08:10:40Z",
    "SearchIntervalEndtimeUtc": "2018-03-26T09:10:40Z",
    "AlertThresholdOperator": "Greater Than",
    "AlertThresholdValue": 0,
    "ResultCount": 2,
    "SearchIntervalInSeconds": 3600,
    "LinkToSearchResults": "https://workspaceID.portal.mms.microsoft.com/#Workspace/search/index?_timeInterval.intervalEnd=2018-03-26T09%3a10%3a40.0000000Z&_timeInterval.intervalDuration=3600&q=Usage",
    "Description": null,
    "Severity": "Warning"
 }
 ```

> [!NOTE]
> Valor do campo de gravidade poderá ser alterado se tiver [mudado a sua preferência de API](alerts-log-api-switch.md) para alertas de registos no Log Analytics.


#### <a name="log-alert-for-azure-application-insights"></a>Alerta de registo para o Azure Application Insights
Segue-se um payload de exemplo para um webhook padrão *sem opção de Json personalizada* quando utilizada para a aplicação com base em insights-alertas de registo.
    
```json
{
    "schemaId":"Microsoft.Insights/LogAlert","data":
    { 
    "SubscriptionId":"12345a-1234b-123c-123d-12345678e",
    "AlertRuleName":"AcmeRule","SearchQuery":"search *",
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
    "SearchIntervalStartTimeUtc": "2018-03-26T08:10:40Z",
    "SearchIntervalEndtimeUtc": "2018-03-26T09:10:40Z",
    "AlertThresholdOperator": "Greater Than",
    "AlertThresholdValue": 0,
    "ResultCount": 2,
    "SearchIntervalInSeconds": 3600,
    "LinkToSearchResults": "https://analytics.applicationinsights.io/subscriptions/12345a-1234b-123c-123d-12345678e/?query=search+*+&timeInterval.intervalEnd=2018-03-26T09%3a10%3a40.0000000Z&_timeInterval.intervalDuration=3600&q=Usage",
    "Description": null,
    "Severity": "3",
    "ApplicationId": "123123f0-01d3-12ab-123f-abc1ab01c0a1"
    }
}
```

#### <a name="log-alert-with-custom-json-payload"></a>Alerta de registo com o Payload JSON personalizado
Por exemplo, para criar um payload personalizado que inclua apenas o nome do alerta e os resultados da pesquisa, pode utilizar o seguinte: 

```json
    {
       "alertname":"#alertrulename",
       "IncludeSearchResults":true
    }
```

Segue-se um payload de exemplo para uma ação personalizada de webhook para qualquer alerta de registo.
    
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
- Saiba mais sobre [alertas de registo nos alertas do Azure](alerts-unified-log.md)
- Compreender [gerir alertas de registo no Azure](alerts-log.md)
- Criar e gerir [grupos de ação no Azure](action-groups.md)
- Saiba mais sobre [Application Insights](../../azure-monitor/app/analytics.md)
- Saiba mais sobre [do Log Analytics](../../azure-monitor/log-query/log-query-overview.md). 

