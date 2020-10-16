---
title: Use Azure Log Analytics para examinar os registos de firewall de aplicação gateway de aplicações de aplicação de aplicação de aplicação de aplicação
description: Este artigo mostra-lhe como pode usar o Azure Log Analytics para examinar os registos de Firewall de aplicação de gateway de aplicação de aplicações
services: web-application-firewall
author: vhorne
ms.service: web-application-firewall
ms.topic: article
ms.date: 09/06/2019
ms.author: victorh
ms.openlocfilehash: 733a9af589718e9abe8736491023003744428cd6
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/09/2020
ms.locfileid: "91361055"
---
# <a name="use-log-analytics-to-examine-application-gateway-web-application-firewall-waf-logs"></a>Use Log Analytics para examinar os registos de Firewall de Aplicação Web do Gateway (WAF)

Uma vez que o seu Gateway DE Aplicação WAF esteja operacional, pode permitir que os registos verifiquem o que se passa a cada pedido. Os registos de firewall dão uma visão do que a WAF está a avaliar, combinar e bloquear. Com o Azure Monitor Log Analytics, pode examinar os dados dentro dos registos de firewall para dar ainda mais informações. Para obter mais informações sobre a criação de um espaço de trabalho Log Analytics, consulte [Criar um espaço de trabalho Log Analytics no portal Azure.](../../azure-monitor/learn/quick-create-workspace.md) Para obter mais informações sobre consultas de registo, consulte [a visão geral das consultas de registo no Azure Monitor](../../azure-monitor/log-query/log-query-overview.md).

## <a name="import-waf-logs"></a>Registos WAF de importação

Para importar os seus registos de firewall em Log Analytics, consulte [a saúde back-end, registos de recursos e métricas para o Gateway de Aplicações](../../application-gateway/application-gateway-diagnostics.md#diagnostic-logging). Quando tiver os registos de firewall no seu espaço de trabalho Log Analytics, pode visualizar dados, escrever consultas, criar visualizações e adicioná-los ao painel de instrumentos do portal.

## <a name="explore-data-with-examples"></a>Explore dados com exemplos

Para visualizar os dados brutos no registo de firewall, pode executar a seguinte consulta:

```
AzureDiagnostics 
| where ResourceProvider == "MICROSOFT.NETWORK" and Category == "ApplicationGatewayFirewallLog"
```

Isto será semelhante à seguinte consulta:

![Consulta log analytics](../media/log-analytics/log-query.png)

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

![A screenshot mostra um dashboard Azure onde pode adicionar a sua consulta.](../media/log-analytics/dashboard.png)

## <a name="next-steps"></a>Passos seguintes

[Saúde back-end, registos de recursos e métricas para Application Gateway](../../application-gateway/application-gateway-diagnostics.md)