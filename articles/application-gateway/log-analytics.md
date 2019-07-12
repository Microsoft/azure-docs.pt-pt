---
title: Utilizar o Azure Log Analytics para examinar registos de Firewall de aplicações da Web de Gateway de aplicação
description: Este artigo mostra-lhe como pode utilizar o Azure Log Analytics para examinar registos de Firewall de aplicações da Web de Gateway de aplicação
services: application-gateway
author: vhorne
ms.service: application-gateway
ms.topic: article
ms.date: 7/10/2019
ms.author: victorh
ms.openlocfilehash: aa867e33ef0faa96b6a66a9075a3a5b8b0b0bca4
ms.sourcegitcommit: c105ccb7cfae6ee87f50f099a1c035623a2e239b
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 07/09/2019
ms.locfileid: "67712179"
---
# <a name="use-log-analytics-to-examine-application-gateway-web-application-firewall-logs"></a>Utilizar o Log Analytics para examinar registos de Firewall de aplicação de Web de Gateway de aplicação

Depois da WAF do Gateway de aplicação está operacional, pode ativar os registos para inspecionar o que está acontecendo com cada solicitação. Informações de conceder de registos de firewall para quais WAF é avaliar, correspondência e a bloquear. Com o Log Analytics, pode examinar os dados contidos nos registos de firewall para dar informações ainda mais. Para obter mais informações sobre como criar uma área de trabalho do Log Analytics, consulte [criar uma área de trabalho do Log Analytics no portal do Azure](../azure-monitor/learn/quick-create-workspace.md). Para obter mais informações sobre consultas de registo, consulte [descrição geral do registo de consultas no Azure Monitor](../azure-monitor/log-query/log-query-overview.md).

## <a name="import-waf-logs"></a>Importar registos WAF

Para importar os seus registos de firewall para o Log Analytics, consulte [estado de funcionamento do Back-end, registos de diagnóstico e métricas para o Gateway de aplicação](application-gateway-diagnostics.md#diagnostic-logging). Quando tiver os registos de firewall na sua área de trabalho do Log Analytics, pode ver os dados, escrever consultas, criar visualizações e adicioná-las ao dashboard do portal.

## <a name="explore-data-with-examples"></a>Explorar os dados com exemplos

Para ver os dados não processados no log de firewall, pode executar a seguinte consulta:

```
AzureDiagnostics 
| where ResourceProvider == "MICROSOFT.NETWORK" and Category == "ApplicationGatewayFirewallLog"
```

Isso terá um aspeto semelhante à seguinte consulta:

![Consulta do log Analytics](media/log-analytics/log-query.png)

Pode desagregar os dados e plotar gráficos ou criar visualizações a partir daqui. Consulte as seguintes consultas como um ponto de partida:

### <a name="matchedblocked-requests-by-ip"></a>Pedidos de correspondido/bloqueado por IP

```
AzureDiagnostics
| where ResourceProvider == "MICROSOFT.NETWORK" and Category == "ApplicationGatewayFirewallLog"
| summarize count() by clientIp_s, bin(TimeGenerated, 1m)
| render timechart
```

### <a name="matchedblocked-requests-by-uri"></a>Pedidos de correspondido/bloqueado pelo URI

```
AzureDiagnostics
| where ResourceProvider == "MICROSOFT.NETWORK" and Category == "ApplicationGatewayFirewallLog"
| summarize count() by requestUri_s, bin(TimeGenerated, 1m)
| render timechart
```

### <a name="top-matched-rules"></a>Principais regras correspondentes

```
AzureDiagnostics
| where ResourceProvider == "MICROSOFT.NETWORK" and Category == "ApplicationGatewayFirewallLog"
| summarize count() by ruleId_s, bin(TimeGenerated, 1m)
| where count_ > 10
| render timechart
```

### <a name="top-five-matched-rule-groups"></a>Principais cinco grupos de regras correspondentes

```
AzureDiagnostics
| where ResourceProvider == "MICROSOFT.NETWORK" and Category == "ApplicationGatewayFirewallLog"
| summarize Count=count() by details_file_s, action_s
| top 5 by Count desc
| render piechart
```

## <a name="add-to-your-dashboard"></a>Adicionar ao dashboard

Depois de criar uma consulta, pode adicioná-lo ao seu dashboard.  Selecione o **afixar ao dashboard** no canto superior direito da área de trabalho de análise de registo. Com as consultas anteriores de quatro afixadas a um dashboard de exemplo, isto é os dados que pode ver rapidamente:

![Dashboard](media/log-analytics/dashboard.png)

## <a name="next-steps"></a>Passos Seguintes

[Estado de funcionamento do back-end, registos de diagnóstico e métricas para o Gateway de aplicação](application-gateway-diagnostics.md)
