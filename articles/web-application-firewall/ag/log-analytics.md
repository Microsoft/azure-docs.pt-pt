---
title: Utilize o Azure Log Analytics para examinar os registos de firewall da aplicação gateway da aplicação
description: Este artigo mostra-lhe como pode usar o Azure Log Analytics para examinar os registos de firewall da aplicação gateway Web Application
services: web-application-firewall
author: vhorne
ms.service: web-application-firewall
ms.topic: article
ms.date: 09/06/2019
ms.author: victorh
ms.openlocfilehash: 4d6b984fd451a835afaa51482f0158e9f315f8fc
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/28/2020
ms.locfileid: "82130921"
---
# <a name="use-log-analytics-to-examine-application-gateway-web-application-firewall-logs"></a>Utilizar o Log Analytics para examinar os Registos da Firewall de Aplicações Web do Gateway de Aplicação

Uma vez que o seu Gateway WAF de aplicação esteja operacional, pode ativar registos para inspecionar o que está a acontecer com cada pedido. Os registos de firewall dão uma ideia do que o WAF está a avaliar, combinar e bloquear. Com o Azure Monitor Log Analytics, pode examinar os dados dentro dos registos de firewall para dar ainda mais informações. Para obter mais informações sobre a criação de um espaço de trabalho log Analytics, consulte [Create a Log Analytics workspace no portal Azure](../../azure-monitor/learn/quick-create-workspace.md). Para mais informações sobre consultas de registo, consulte a [visão geral das consultas de registo no Monitor Azure](../../azure-monitor/log-query/log-query-overview.md).

## <a name="import-waf-logs"></a>Importar registos WAF

Para importar os seus registos de firewall para log Analytics, consulte a [saúde de back-end, registos](../../application-gateway/application-gateway-diagnostics.md#diagnostic-logging)de recursos e métricas para O Gateway da Aplicação . Quando tiver os registos de firewall no seu espaço de trabalho Log Analytics, pode ver dados, escrever consultas, criar visualizações e adicioná-los ao seu portal dashboard.

## <a name="explore-data-with-examples"></a>Explore dados com exemplos

Para ver os dados brutos no registo de firewall, pode executar a seguinte consulta:

```
AzureDiagnostics 
| where ResourceProvider == "MICROSOFT.NETWORK" and Category == "ApplicationGatewayFirewallLog"
```

Isto será semelhante à seguinte consulta:

![Consulta de Log Analytics](../media/log-analytics/log-query.png)

Você pode perfurar os dados, e traçar gráficos ou criar visualizações a partir daqui. Consulte as seguintes consultas como ponto de partida:

### <a name="matchedblocked-requests-by-ip"></a>Pedidos combinados/bloqueados por IP

```
AzureDiagnostics
| where ResourceProvider == "MICROSOFT.NETWORK" and Category == "ApplicationGatewayFirewallLog"
| summarize count() by clientIp_s, bin(TimeGenerated, 1m)
| render timechart
```

### <a name="matchedblocked-requests-by-uri"></a>Pedidos combinados/bloqueados por URI

```
AzureDiagnostics
| where ResourceProvider == "MICROSOFT.NETWORK" and Category == "ApplicationGatewayFirewallLog"
| summarize count() by requestUri_s, bin(TimeGenerated, 1m)
| render timechart
```

### <a name="top-matched-rules"></a>Regras mais compatíveis

```
AzureDiagnostics
| where ResourceProvider == "MICROSOFT.NETWORK" and Category == "ApplicationGatewayFirewallLog"
| summarize count() by ruleId_s, bin(TimeGenerated, 1m)
| where count_ > 10
| render timechart
```

### <a name="top-five-matched-rule-groups"></a>Os cinco principais grupos de regras combinados

```
AzureDiagnostics
| where ResourceProvider == "MICROSOFT.NETWORK" and Category == "ApplicationGatewayFirewallLog"
| summarize Count=count() by details_file_s, action_s
| top 5 by Count desc
| render piechart
```

## <a name="add-to-your-dashboard"></a>Adicione ao seu painel de instrumentos

Assim que criar uma consulta, pode adicioná-la ao seu painel de instrumentos.  Selecione o **Pin para painel** na parte superior direita do espaço de trabalho de análise de registo. Com as quatro consultas anteriores fixadas num dashboard de exemplo, estes são os dados que pode ver num ápice:

![Dashboard](../media/log-analytics/dashboard.png)

## <a name="next-steps"></a>Passos seguintes

[Saúde de back-end, registos de recursos e métricas para Gateway de Aplicação](../../application-gateway/application-gateway-diagnostics.md)