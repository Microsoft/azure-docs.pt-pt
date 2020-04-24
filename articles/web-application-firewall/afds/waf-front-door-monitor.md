---
title: Monitorização e exploração madeireira de firewall de aplicação web Azure
description: Aprenda firewall de aplicação web (WAF) com monitorização frontdoor e registo
author: vhorne
ms.service: web-application-firewall
ms.topic: article
services: web-application-firewall
ms.date: 08/21/2019
ms.author: victorh
ms.openlocfilehash: b4f666415a96307b89022c6caf6af90581f294f3
ms.sourcegitcommit: f7d057377d2b1b8ee698579af151bcc0884b32b4
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/24/2020
ms.locfileid: "82115368"
---
# <a name="azure-web-application-firewall-monitoring-and-logging"></a>Monitorização e exploração madeireira de firewall de aplicação web Azure 

A monitorização e exploração madeireira azure Web Application Firewall (WAF) são fornecidas através da exploração madeireira e integração com registos do Monitor Azure e do Monitor Azure.

## <a name="azure-monitor"></a>Azure Monitor

WaF com log FrontDoor está integrado com [o Monitor Azure](../../azure-monitor/overview.md). O Monitor Azure permite-lhe rastrear informações de diagnóstico, incluindo alertas waf e registos. Pode configurar a monitorização waf dentro do recurso Porta Frontal no portal sob o separador **Diagnóstico ou** através do serviço Azure Monitor diretamente.

Do portal Azure, vá para o tipo de recurso da Porta Da Frente. A partir do separador**Métricas** de **Monitorização**/à esquerda, pode adicionar **webApplicationFirewallRequestCount** para rastrear o número de pedidos que correspondem às regras waf. Filtros personalizados podem ser criados com base em tipos de ação e nomes de regras.

![WAFMetrics](../media/waf-frontdoor-monitor/waf-frontdoor-metrics.png)

## <a name="logs-and-diagnostics"></a>Registos e diagnósticos

WaF com Porta Frontal fornece relatórios detalhados sobre cada ameaça que deteta. Os registos estão integrados no Diagnóstico do Azure e os registos e alertas são registados no formato json. Estes registos podem ser integrados com [registos do Monitor Azure](../../azure-monitor/insights/azure-networking-analytics.md).

![WAFDiag](../media/waf-frontdoor-monitor/waf-frontdoor-diagnostics.png)

FrontdoorAccessLog regista todos os pedidos que são encaminhados para back-ends do cliente. FrontdoorWebApplicationFirewallLog regista qualquer pedido que corresponda a uma regra WAF.

A seguinte consulta de exemplo obtém registos waf em pedidos bloqueados:

``` WAFlogQuery
AzureDiagnostics
| where ResourceType == "FRONTDOORS" and Category == "FrontdoorWebApplicationFirewallLog"
| where action_s == "Block"

```

Aqui está um exemplo de um pedido registado no registo WAF:

``` WAFlogQuerySample
{
    "PreciseTimeStamp": "2020-01-25T00:11:19.3866091Z",
    "time": "2020-01-25T00:11:19.3866091Z",
    "category": "FrontdoorWebApplicationFirewallLog",
    "operationName": "Microsoft.Network/FrontDoor/WebApplicationFirewallLog/Write",
    "properties": {
        "clientIP": "xx.xx.xxx.xxx",
        "socketIP": "xx.xx.xxx.xxx",
        "requestUri": "https://wafdemofrontdoorwebapp.azurefd.net:443/?q=../../x",
        "ruleName": "Microsoft_DefaultRuleSet-1.1-LFI-930100",
        "policy": "WafDemoCustomPolicy",
        "action": "Block",
        "host": "wafdemofrontdoorwebapp.azurefd.net",
        "refString": "0p4crXgAAAABgMq5aIpu0T6AUfCYOroltV1NURURHRTA2MTMANjMxNTAwZDAtOTRiNS00YzIwLTljY2YtNjFhNzMyOWQyYTgy",
        "policyMode": "prevention"
    }
}

``` 

A consulta de exemplo seguinte obtém entradas de AccessLogs:

``` AccessLogQuery
AzureDiagnostics
| where ResourceType == "FRONTDOORS" and Category == "FrontdoorAccessLog"

```

Aqui está um exemplo de um pedido registado no registo de Acesso:

``` AccessLogSample
{
    "PreciseTimeStamp": "2020-01-25T00:11:12.0160150Z",
    "time": "2020-01-25T00:11:12.0160150Z",
    "category": "FrontdoorAccessLog",
    "operationName": "Microsoft.Network/FrontDoor/AccessLog/Write",
    "properties": {
        "trackingReference": "0n4crXgAAAACnRKbdALbyToAqNfSHssDvV1NURURHRTA2MTMANjMxNTAwZDAtOTRiNS00YzIwLTljY2YtNjFhNzMyOWQyYTgy",
        "httpMethod": "GET",
        "httpVersion": "2.0",
        "requestUri": "https://wafdemofrontdoorwebapp.azurefd.net:443/",
        "requestBytes": "710",
        "responseBytes": "3116",
        "userAgent": "Mozilla/5.0 (Windows NT 10.0; Win64; x64) AppleWebKit/537.36 (KHTML, like Gecko) Chrome/81.0.4017.0 Safari/537.36 Edg/81.0.389.2",
        "clientIp": "xx.xx.xxx.xxx",
        "timeTaken": "0.598",
        "securityProtocol": "TLS 1.2",
        "routingRuleName": "WAFdemoWebAppRouting",
        "backendHostname": "wafdemouksouth.azurewebsites.net:443",
        "sentToOriginShield": false,
        "httpStatusCode": "200",
        "httpStatusDetails": "200"
    }
}

```

## <a name="next-steps"></a>Passos seguintes

- Saiba mais sobre [a Porta da Frente.](../../frontdoor/front-door-overview.md)
