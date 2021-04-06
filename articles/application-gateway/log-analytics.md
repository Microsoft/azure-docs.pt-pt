---
title: Examine os registos da WAF utilizando o Azure Log Analytics
titleSuffix: Azure Application Gateway
description: Este artigo mostra-lhe como pode utilizar o Azure Log Analytics para examinar os registos da Application Gateway Web Application Firewall (WAF).
services: application-gateway
author: vhorne
ms.service: application-gateway
ms.topic: troubleshooting
ms.date: 11/14/2019
ms.author: victorh
ms.openlocfilehash: 6c7acdda3dce3b1338888cdf36f1623597c11fc3
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/29/2021
ms.locfileid: "100586074"
---
# <a name="use-log-analytics-to-examine-application-gateway-web-application-firewall-waf-logs"></a>Use Log Analytics para examinar os registos de firewall de aplicação web do Gateway (WAF)

Uma vez que o seu Gateway DE Aplicação WAF esteja operacional, pode permitir que os registos verifiquem o que se passa a cada pedido. Os registos de firewall dão uma visão do que a WAF está a avaliar, combinar e bloquear. Com o Log Analytics, pode examinar os dados dentro dos registos de firewall para dar ainda mais informações. Para obter mais informações sobre a criação de um espaço de trabalho Log Analytics, consulte [Criar um espaço de trabalho Log Analytics no portal Azure.](../azure-monitor/logs/quick-create-workspace.md) Para obter mais informações sobre consultas de registo, consulte [a visão geral das consultas de registo no Azure Monitor](../azure-monitor/logs/log-query-overview.md).

## <a name="import-waf-logs"></a>Registos WAF de importação

Para importar os seus registos de firewall em Log Analytics, consulte [saúde back-end, registos de diagnóstico e métricas para o Gateway de Aplicações](application-gateway-diagnostics.md#diagnostic-logging). Quando tiver os registos de firewall no seu espaço de trabalho Log Analytics, pode visualizar dados, escrever consultas, criar visualizações e adicioná-los ao painel de instrumentos do portal.

## <a name="explore-data-with-examples"></a>Explore dados com exemplos

Para visualizar os dados brutos no registo de firewall, pode executar a seguinte consulta:

```
AzureDiagnostics 
| where ResourceProvider == "MICROSOFT.NETWORK" and Category == "ApplicationGatewayFirewallLog"
```

Isto será semelhante à seguinte consulta:

![Consulta log analytics](media/log-analytics/log-query.png)

Pode perfurar os dados, traçar gráficos ou criar visualizações a partir daqui. Veja as seguintes consultas como ponto de partida:

### <a name="matchedblocked-requests-by-ip"></a>Pedidos combinados/bloqueados por IP

```
AzureDiagnostics
| where ResourceProvider == "MICROSOFT.NETWORK" and Category == "ApplicationGatewayFirewallLog"
| summarize count() by clientIp_s, bin(TimeGenerated, 1m)
| render timechart
```

### <a name="matchedblocked-requests-by-uri"></a>Pedidos combinados/bloqueados pela URI

```
AzureDiagnostics
| where ResourceProvider == "MICROSOFT.NETWORK" and Category == "ApplicationGatewayFirewallLog"
| summarize count() by requestUri_s, bin(TimeGenerated, 1m)
| render timechart
```

### <a name="top-matched-rules"></a>Regras de topo combinados

```
AzureDiagnostics
| where ResourceProvider == "MICROSOFT.NETWORK" and Category == "ApplicationGatewayFirewallLog"
| summarize count() by ruleId_s, bin(TimeGenerated, 1m)
| where count_ > 10
| render timechart
```

### <a name="top-five-matched-rule-groups"></a>Top cinco grupos de regras combinados

```
AzureDiagnostics
| where ResourceProvider == "MICROSOFT.NETWORK" and Category == "ApplicationGatewayFirewallLog"
| summarize Count=count() by details_file_s, action_s
| top 5 by Count desc
| render piechart
```

## <a name="add-to-your-dashboard"></a>Adicione ao seu painel

Uma vez que crie uma consulta, pode adicioná-la ao seu painel de instrumentos.  Selecione o Pin para o painel de **instrumentos** no espaço de trabalho de análise de registo. Com as quatro consultas anteriores fixadas a um painel de exemplo, estes são os dados que pode ver num ápice:

![A screenshot mostra um dashboard Azure onde pode adicionar a sua consulta.](media/log-analytics/dashboard.png)

## <a name="next-steps"></a>Passos seguintes

[Back-end health, diagnostic logs, and metrics for Application Gateway](application-gateway-diagnostics.md) (Estado de funcionamento do back-end, registos de diagnóstico e métricas para o Gateway de Aplicação)
