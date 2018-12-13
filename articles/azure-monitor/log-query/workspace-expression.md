---
title: expressão de Workspace() na consulta do Log Analytics do Azure | Documentos da Microsoft
description: A expressão de área de trabalho é utilizada numa consulta do Log Analytics para recuperar dados de uma área de trabalho específica no mesmo grupo de recursos, outro grupo de recursos ou outra subscrição.
services: log-analytics
documentationcenter: ''
author: bwren
manager: carmonm
editor: ''
ms.assetid: ''
ms.service: log-analytics
ms.workload: na
ms.tgt_pltfrm: na
ms.topic: article
ms.date: 09/10/2018
ms.author: bwren
ms.openlocfilehash: 24a737a728b0a249fda76cbff481bea284ac24aa
ms.sourcegitcommit: 5b869779fb99d51c1c288bc7122429a3d22a0363
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 12/10/2018
ms.locfileid: "53182949"
---
# <a name="workspace-expression-in-log-analytics-query"></a>expressão de Workspace() na consulta do Log Analytics

O `workspace` expressão é utilizada numa consulta do Log Analytics para recuperar dados de uma área de trabalho específica no mesmo grupo de recursos, outro grupo de recursos ou outra subscrição. Isto é útil para incluir dados de registo numa consulta do Application Insights e para consultar dados em várias áreas de trabalho numa consulta de registo.


## <a name="syntax"></a>Sintaxe

`workspace(`*Identificador*`)`

## <a name="arguments"></a>Argumentos

- *Identificador*: Identifica a área de trabalho utilizando um dos formatos na tabela abaixo.

| Identificador | Descrição | Exemplo
|:---|:---|:---|
| Nome do Recurso | Nome legível por humano da área de trabalho (também conhecidas como "nome do componente") | Workspace("contosoretail") |
| Nome qualificado | Nome completo da área de trabalho no formato: "subscriptionName/resourceGroup/componentName" | Workspace('Contoso/ContosoResource/ContosoWorkspace') |
| ID | GUID da área de trabalho | Workspace("b438b3f6-912a-46d5-9db1-b42069242ab4") |
| ID de recurso do Azure | Identificador para o recurso do Azure | Workspace("/subscriptions/e4227-645-44e-9c67-3b84b5982/resourcegroups/ContosoAzureHQ/Providers/Microsoft.OperationalInsights/Workspaces/contosoretail") |


## <a name="notes"></a>Notas

* Tem de ter acesso de leitura para a área de trabalho.
* Uma expressão relacionada é `app` que permite consultar em todas as aplicações do Application Insights.

## <a name="examples"></a>Exemplos

```Kusto
workspace("contosoretail").Update | count
```
```Kusto
workspace("b438b4f6-912a-46d5-9cb1-b44069212ab4").Update | count
```
```Kusto
workspace("/subscriptions/e427267-5645-4c4e-9c67-3b84b59a6982/resourcegroups/ContosoAzureHQ/providers/Microsoft.OperationalInsights/workspaces/contosoretail").Event | count
```
```Kusto
union 
(workspace("myworkspace").Heartbeat | where Computer contains "Con"),
(app("myapplication").requests | where cloud_RoleInstance contains "Con")
| count  
```
```Kusto
union 
(workspace("myworkspace").Heartbeat), (app("myapplication").requests)
| where TimeGenerated between(todatetime("2018-02-08 15:00:00") .. todatetime("2018-12-08 15:05:00"))
```

## <a name="next-steps"></a>Passos Seguintes

- Consulte a [expressão de aplicação](workspace-expression.md) referir-se a aplicação Application Insights.
- Saiba mais sobre como [dados do Log Analytics](../../azure-monitor/log-query/log-query-overview.md) são armazenados.