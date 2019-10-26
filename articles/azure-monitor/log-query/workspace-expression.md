---
title: expressão de espaço de trabalho () na consulta de log de Azure Monitor | Microsoft Docs
description: A expressão de espaço de trabalho é usada em uma consulta de Azure Monitor log para recuperar dados de um espaço de trabalho específico no mesmo grupo de recursos, outro grupo de recursos ou outra assinatura.
ms.service: azure-monitor
ms.subservice: logs
ms.topic: conceptual
author: bwren
ms.author: bwren
ms.date: 09/10/2018
ms.openlocfilehash: f26b9534fbf95cc301ae782d47ab7030988fa469
ms.sourcegitcommit: 4c3d6c2657ae714f4a042f2c078cf1b0ad20b3a4
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/25/2019
ms.locfileid: "72932840"
---
# <a name="workspace-expression-in-azure-monitor-log-query"></a>expressão de espaço de trabalho () na consulta de log de Azure Monitor

A expressão `workspace` é usada em uma consulta Azure Monitor para recuperar dados de um espaço de trabalho específico no mesmo grupo de recursos, em outro grupo de recursos ou em outra assinatura. Isso é útil para incluir dados de log em uma consulta Application Insights e consultar dados entre vários espaços de trabalho em uma consulta de log.


## <a name="syntax"></a>Sintaxe

*identificador* de `workspace(``)`

## <a name="arguments"></a>Argumentos

- *Identificador*: identifica o espaço de trabalho usando um dos formatos na tabela a seguir.

| Identificador | Descrição | Exemplo
|:---|:---|:---|
| Nome do Recurso | Nome legível por humanos do espaço de trabalho (também conhecido como "nome do componente") | espaço de trabalho ("contosoretail") |
| Nome qualificado | Nome completo do espaço de trabalho no formato: "subscriptionname/resourcegroup/ComponentName" | espaço de trabalho (' contoso/ContosoResource/ContosoWorkspace ') |
| ID | GUID do espaço de trabalho | espaço de trabalho ("b438b3f6-912a-46D5-9db1-b42069242ab4") |
| ID de recurso do Azure | Identificador para o recurso do Azure | espaço de trabalho ("/subscriptions/e4227-645-44e-9c67-3b84b5982/resourcegroups/ContosoAzureHQ/providers/Microsoft.OperationalInsights/workspaces/contosoretail") |


## <a name="notes"></a>Notas

* Você deve ter acesso de leitura ao espaço de trabalho.
* Uma expressão relacionada é `app` que permite consultar em aplicativos Application Insights.

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

## <a name="next-steps"></a>Passos seguintes

- Consulte a [expressão do aplicativo](app-expression.md) para se referir a um aplicativo Application insights.
- Leia sobre como [Azure monitor dados](log-query-overview.md) são armazenados.
- Acesse a documentação completa para a [linguagem de consulta Kusto](/azure/kusto/query/).
