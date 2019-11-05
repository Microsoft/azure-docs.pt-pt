---
title: Monitoramento e registro em log do firewall do aplicativo Web do Azure
description: Saiba mais sobre o WAF (firewall do aplicativo Web) com o monitoramento e registro em log do FrontDoor
author: vhorne
ms.service: web-application-firewall
ms.topic: article
services: web-application-firewall
ms.date: 08/21/2019
ms.author: victorh
ms.openlocfilehash: 0c705139c082f13f40362e598f0fda9ba0a128a5
ms.sourcegitcommit: c22327552d62f88aeaa321189f9b9a631525027c
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 11/04/2019
ms.locfileid: "73512486"
---
# <a name="azure-web-application-firewall-monitoring-and-logging"></a>Monitoramento e registro em log do firewall do aplicativo Web do Azure 

O monitoramento e o registro em log do WAF (firewall do aplicativo Web) do Azure são fornecidos por meio de log e integração com logs de Azure Monitor e Azure Monitor.

## <a name="azure-monitor"></a>Azure Monitor

O WAF com o log do FrontDoor é integrado ao [Azure monitor](../../azure-monitor/overview.md). Azure Monitor permite que você acompanhe informações de diagnóstico, incluindo alertas e logs do WAF. Você pode configurar o monitoramento de WAF dentro do recurso de porta frontal no portal na guia **diagnóstico** ou por meio do serviço de Azure monitor diretamente.

Em portal do Azure, vá para tipo de recurso da porta frontal. Na guia **monitoramento**/**métricas** à esquerda, você pode adicionar **WebApplicationFirewallRequestCount** para rastrear o número de solicitações que correspondem às regras de WAF. Os filtros personalizados podem ser criados com base em tipos de ação e nomes de regra.

![WAFMetrics](../media/waf-frontdoor-monitor/waf-frontdoor-metrics.png)

## <a name="logs-and-diagnostics"></a>Logs e diagnósticos

WAF com a porta frontal fornece relatórios detalhados sobre cada ameaça detectada. Os registos estão integrados no Diagnóstico do Azure e os registos e alertas são registados no formato json. Esses logs podem ser integrados a [logs de Azure monitor](../../azure-monitor/insights/azure-networking-analytics.md).

![WAFDiag](../media/waf-frontdoor-monitor/waf-frontdoor-diagnostics.png)

FrontdoorAccessLog registra todas as solicitações que são encaminhadas para back-ends do cliente. FrontdoorWebApplicationFirewallLog registra qualquer solicitação que corresponda a uma regra de WAF.

A consulta de exemplo a seguir obtém logs do WAF em solicitações bloqueadas:

``` WAFlogQuery
AzureDiagnostics
| where ResourceType == "FRONTDOORS" and Category == "FrontdoorWebApplicationFirewallLog"
| where action_s == "Block"

```

A consulta de exemplo a seguir obtém entradas AccessLogs:

``` AccessLogQuery
AzureDiagnostics
| where ResourceType == "FRONTDOORS" and Category == "FrontdoorAccessLog"


```

## <a name="next-steps"></a>Passos seguintes

- Saiba mais sobre a [porta da frente](../../frontdoor/front-door-overview.md).

