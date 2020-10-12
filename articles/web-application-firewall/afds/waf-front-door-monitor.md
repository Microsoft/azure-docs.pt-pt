---
title: Monitorização e registo de firewall de aplicação web Azure
description: Aprenda Firewall de Aplicação Web (WAF) com monitorização e registo frontal
author: vhorne
ms.service: web-application-firewall
ms.topic: article
services: web-application-firewall
ms.date: 06/09/2020
ms.author: victorh
ms.openlocfilehash: 596374d4f3f188e08a10bd25b36b178cc79a6e57
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/09/2020
ms.locfileid: "84808958"
---
# <a name="azure-web-application-firewall-monitoring-and-logging"></a>Monitorização e registo de firewall de aplicação web Azure

A monitorização e registo de registos do Azure Web Application Firewall (WAF) são fornecidos através de registos e integração com registos Azure Monitor e Azure Monitor.

## <a name="azure-monitor"></a>Azure Monitor

O waf com log FrontDoor está integrado com [o Azure Monitor](../../azure-monitor/overview.md). O Azure Monitor permite-lhe rastrear informações de diagnóstico, incluindo alertas e registos waf. Pode configurar a monitorização da WAF dentro do recurso Porta Frontal no portal sob o separador **Diagnóstico ou** através do serviço Azure Monitor diretamente.

A partir do portal Azure, vá para o tipo de recurso Front Door. A partir do **separador Métricas de Monitorização** / **Metrics** à esquerda, pode adicionar **WebApplicationFirewallRequestCount** para rastrear o número de pedidos que correspondem às regras da WAF. Os filtros personalizados podem ser criados com base em tipos de ação e nomes de regras.

:::image type="content" source="../media/waf-frontdoor-monitor/waf-frontdoor-metrics.png" alt-text="WAFMetrics ":::

## <a name="logs-and-diagnostics"></a>Registos e diagnósticos

A WAF com porta frontal fornece relatórios detalhados sobre cada ameaça que deteta. Os registos estão integrados no Diagnóstico do Azure e os registos e alertas são registados no formato json. Estes registos podem ser integrados com [registos do Azure Monitor](../../azure-monitor/insights/azure-networking-analytics.md).

![WAFDiag](../media/waf-frontdoor-monitor/waf-frontdoor-diagnostics.png)

[FrontdoorAccessLog](../../frontdoor/front-door-diagnostics.md) regista todos os pedidos. FrontdoorWebApplicationFirewallLog regista qualquer pedido que corresponda a uma regra WAF com o esquema abaixo:

| Propriedade  | Descrição |
| ------------- | ------------- |
|Ação|Medidas tomadas no pedido|
| ClientIp | O endereço IP do cliente que fez o pedido. Se houve um cabeçalho X-Forwarded-For no pedido, então o IP do Cliente é escolhido no campo de cabeçalho. |
| ClientPort | A porta IP do cliente que fez o pedido. |
| Detalhes|Detalhes adicionais sobre o pedido combinado |
|| matchVariableName: http nome do parâmetro do pedido coincide, por exemplo, com nomes de cabeçalho|
|| matchVariableValue: valores que desencadearam a partida|
| Anfitrião | O cabeçalho anfitrião do pedido combinado |
| Política | O nome da política da WAF que o pedido correspondia. |
| PolicyMode | Modo de operações da política da WAF. Os valores possíveis são "Prevenção" e "Deteção" |
| RequestUri | URI completo do pedido combinado. |
| Nome de Regras | O nome da regra da WAF que o pedido correspondia. |
| SocketIp | O endereço IP de origem visto pela WAF. Este endereço IP baseia-se na sessão TCP, independente de quaisquer cabeçalhos de pedido.|
| TrackingReference | A cadeia de referência única que identifica um pedido servido pela Porta da Frente, também enviada como cabeçalho X-Azure-Ref para o cliente. Necessário para pesquisar detalhes nos registos de acesso para um pedido específico. |

O exemplo de consulta que se segue devolve os registos da WAF em pedidos bloqueados:

``` WAFlogQuery
AzureDiagnostics
| where ResourceType == "FRONTDOORS" and Category == "FrontdoorWebApplicationFirewallLog"
| where action_s == "Block"

```

Aqui está um exemplo de um pedido registado no registo WAF:

``` WAFlogQuerySample
{
    "time":  "2020-06-09T22:32:17.8376810Z",
    "category": "FrontdoorWebApplicationFirewallLog",
    "operationName": "Microsoft.Network/FrontDoorWebApplicationFirewallLog/Write",
    "properties":
    {
        "clientIP":"xxx.xxx.xxx.xxx",
        "clientPort":"52097",
        "socketIP":"xxx.xxx.xxx.xxx",
        "requestUri":"https://wafdemofrontdoorwebapp.azurefd.net:443/?q=%27%20or%201=1",
        "ruleName":"Microsoft_DefaultRuleSet-1.1-SQLI-942100",
        "policy":"WafDemoCustomPolicy",
        "action":"Block",
        "host":"wafdemofrontdoorwebapp.azurefd.net",
        "trackingReference":"08Q3gXgAAAAAe0s71BET/QYwmqtpHO7uAU0pDRURHRTA1MDgANjMxNTAwZDAtOTRiNS00YzIwLTljY2YtNjFhNzMyOWQyYTgy",
        "policyMode":"prevention",
        "details":
            {
            "matches":
                [{
                "matchVariableName":"QueryParamValue:q",
                "matchVariableValue":"' or 1=1"
                }]
            }
     }
}
```

A seguinte consulta de exemplo devolve entradas de AccessLogs:

``` AccessLogQuery
AzureDiagnostics
| where ResourceType == "FRONTDOORS" and Category == "FrontdoorAccessLog"

```

Aqui está um exemplo de um pedido registado no registo de acesso:

``` AccessLogSample
{
"time": "2020-06-09T22:32:17.8383427Z",
"category": "FrontdoorAccessLog",
"operationName": "Microsoft.Network/FrontDoor/AccessLog/Write",
 "properties":
    {
    "trackingReference":"08Q3gXgAAAAAe0s71BET/QYwmqtpHO7uAU0pDRURHRTA1MDgANjMxNTAwZDAtOTRiNS00YzIwLTljY2YtNjFhNzMyOWQyYTgy",
    "httpMethod":"GET",
    "httpVersion":"2.0",
    "requestUri":"https://wafdemofrontdoorwebapp.azurefd.net:443/?q=%27%20or%201=1",
    "requestBytes":"715",
    "responseBytes":"380",
    "userAgent":"Mozilla/5.0 (Windows NT 10.0; Win64; x64) AppleWebKit/537.36 (KHTML, like Gecko) Chrome/85.0.4157.0 Safari/537.36 Edg/85.0.531.1",
    "clientIp":"xxx.xxx.xxx.xxx",
    "socketIp":"xxx.xxx.xxx.xxx",
    "clientPort":"52097",
    "timeTaken":"0.003",
    "securityProtocol":"TLS 1.2",
    "routingRuleName":"WAFdemoWebAppRouting",
    "rulesEngineMatchNames":[],
    "backendHostname":"wafdemowebappuscentral.azurewebsites.net:443",
    "sentToOriginShield":false,
    "httpStatusCode":"403",
    "httpStatusDetails":"403",
    "pop":"SJC",
    "cacheStatus":"CONFIG_NOCACHE"
    }
}

```

## <a name="next-steps"></a>Passos seguintes

- Saiba mais sobre [a Porta da Frente.](../../frontdoor/front-door-overview.md)
