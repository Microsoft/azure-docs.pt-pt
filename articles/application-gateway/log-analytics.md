---
title: Examinar logs do WAF usando o Azure Log Analytics
titleSuffix: Azure Application Gateway
description: Este artigo mostra como você pode usar o Azure Log Analytics para examinar os logs de firewall do aplicativo Web do gateway de aplicativo
services: application-gateway
author: vhorne
ms.service: application-gateway
ms.topic: article
ms.date: 11/14/2019
ms.author: victorh
ms.openlocfilehash: 9fe4462a71852e5f66268f798f6f0418f2dd39c4
ms.sourcegitcommit: b1a8f3ab79c605684336c6e9a45ef2334200844b
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 11/13/2019
ms.locfileid: "74048104"
---
# <a name="use-log-analytics-to-examine-application-gateway-web-application-firewall-logs"></a>Utilizar o Log Analytics para examinar os Registos da Firewall de Aplicações Web do Gateway de Aplicação

Depois que o WAF do gateway de aplicativo estiver operacional, você poderá habilitar os logs para inspecionar o que está acontecendo com cada solicitação. Os logs de firewall fornecem informações sobre o que o WAF está avaliando, correspondendo e bloqueando. Com Log Analytics, você pode examinar os dados nos logs do firewall para fornecer ainda mais informações. Para obter mais informações sobre como criar um espaço de trabalho Log Analytics, consulte [criar um espaço de trabalho log Analytics no portal do Azure](../azure-monitor/learn/quick-create-workspace.md). Para obter mais informações sobre consultas de log, consulte [visão geral das consultas de log no Azure monitor](../azure-monitor/log-query/log-query-overview.md).

## <a name="import-waf-logs"></a>Importar logs do WAF

Para importar os logs de firewall para o Log Analytics, consulte [integridade de back-end, logs de diagnóstico e métricas para o gateway de aplicativo](application-gateway-diagnostics.md#diagnostic-logging). Quando você tiver os logs de firewall em seu espaço de trabalho do Log Analytics, poderá exibir dados, gravar consultas, criar visualizações e adicioná-las ao painel do Portal.

## <a name="explore-data-with-examples"></a>Explorar dados com exemplos

Para exibir os dados brutos no log do firewall, você pode executar a seguinte consulta:

```
AzureDiagnostics 
| where ResourceProvider == "MICROSOFT.NETWORK" and Category == "ApplicationGatewayFirewallLog"
```

Isso será semelhante à seguinte consulta:

![Consulta de Log Analytics](media/log-analytics/log-query.png)

Você pode fazer uma busca detalhada nos dados e plotar grafos ou criar visualizações aqui. Consulte as seguintes consultas como um ponto de partida:

### <a name="matchedblocked-requests-by-ip"></a>Solicitações de correspondência/bloqueadas por IP

```
AzureDiagnostics
| where ResourceProvider == "MICROSOFT.NETWORK" and Category == "ApplicationGatewayFirewallLog"
| summarize count() by clientIp_s, bin(TimeGenerated, 1m)
| render timechart
```

### <a name="matchedblocked-requests-by-uri"></a>Solicitações de correspondência/bloqueadas por URI

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

### <a name="top-five-matched-rule-groups"></a>Cinco principais grupos de regras correspondentes

```
AzureDiagnostics
| where ResourceProvider == "MICROSOFT.NETWORK" and Category == "ApplicationGatewayFirewallLog"
| summarize Count=count() by details_file_s, action_s
| top 5 by Count desc
| render piechart
```

## <a name="add-to-your-dashboard"></a>Adicionar ao seu painel

Depois de criar uma consulta, você pode adicioná-la ao seu painel.  Selecione **fixar no painel** no canto superior direito do espaço de trabalho do log Analytics. Com as quatro consultas anteriores fixadas em um painel de exemplo, esses são os dados que você pode ver em um relance:

![Dashboard](media/log-analytics/dashboard.png)

## <a name="next-steps"></a>Passos seguintes

[Integridade de back-end, logs de diagnóstico e métricas para o gateway de aplicativo](application-gateway-diagnostics.md)
