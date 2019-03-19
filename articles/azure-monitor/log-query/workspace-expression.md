---
title: expressão de Workspace() na consulta de registo do Azure Monitor | Documentos da Microsoft
description: A expressão de área de trabalho é utilizada numa consulta de registo do Azure Monitor para recuperar dados de uma área de trabalho específica no mesmo grupo de recursos, outro grupo de recursos ou outra subscrição.
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
ms.openlocfilehash: b4bc652d54150b72cc64898464b3511a860bf011
ms.sourcegitcommit: 2d0fb4f3fc8086d61e2d8e506d5c2b930ba525a7
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/18/2019
ms.locfileid: "57996298"
---
# <a name="workspace-expression-in-azure-monitor-log-query"></a>expressão de Workspace() na consulta de registo do Azure Monitor

O `workspace` expressão é usada numa consulta do Azure Monitor para recuperar dados de uma área de trabalho específica no mesmo grupo de recursos, outro grupo de recursos ou outra subscrição. Isto é útil para incluir dados de registo numa consulta do Application Insights e para consultar dados em várias áreas de trabalho numa consulta de registo.


## <a name="syntax"></a>Sintaxe

`workspace(`*Identificador*`)`

## <a name="arguments"></a>Argumentos

- *Identificador*: Identifica a área de trabalho utilizando um dos formatos na tabela abaixo.

| Identificador | Descrição | Exemplo
|:---|:---|:---|
| Nome do Recurso | Nome legível por humano da área de trabalho (também conhecidas como "nome do componente") | workspace("contosoretail") |
| Nome qualificado | Nome completo da área de trabalho no formato: "subscriptionName/resourceGroup/componentName" | Workspace('Contoso/ContosoResource/ContosoWorkspace') |
| ID | GUID da área de trabalho | workspace("b438b3f6-912a-46d5-9db1-b42069242ab4") |
| ID de recurso do Azure | Identificador para o recurso do Azure | workspace("/subscriptions/e4227-645-44e-9c67-3b84b5982/resourcegroups/ContosoAzureHQ/providers/Microsoft.OperationalInsights/workspaces/contosoretail") |


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

- Consulte a [expressão de aplicação](app-expression.md) para fazer referência a uma aplicação do Application Insights.
- Saiba mais sobre como [dados do Azure Monitor](log-query-overview.md) são armazenados.
- Aceder a documentação completa para o [linguagem de consulta de Kusto](/azure/kusto/query/).
