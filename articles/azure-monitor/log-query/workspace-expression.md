---
title: expressão do espaço de trabalho na consulta de registo do Monitor Azure [ Microsoft Docs
description: A expressão do espaço de trabalho é usada numa consulta de registo do Monitor Azure para recuperar dados de um espaço de trabalho específico no mesmo grupo de recursos, outro grupo de recursos ou outra subscrição.
ms.subservice: logs
ms.topic: conceptual
author: bwren
ms.author: bwren
ms.date: 09/10/2018
ms.openlocfilehash: 255888acf5da6149b6a964b23ed038b99715481c
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/27/2020
ms.locfileid: "75364956"
---
# <a name="workspace-expression-in-azure-monitor-log-query"></a>expressão do espaço de trabalho na consulta de registo do Monitor Azure

A `workspace` expressão é usada numa consulta do Azure Monitor para recuperar dados de um espaço de trabalho específico no mesmo grupo de recursos, outro grupo de recursos ou outra subscrição. Isto é útil para incluir dados de log em uma consulta de Insights de aplicação e para consultar dados em vários espaços de trabalho em uma consulta de log.


## <a name="syntax"></a>Sintaxe

`workspace(`*Identificador*`)`

## <a name="arguments"></a>Argumentos

- *Identificador*: Identifica o espaço de trabalho utilizando um dos formatos na tabela abaixo.

| Identificador | Descrição | Exemplo
|:---|:---|:---|
| Nome do Recurso | Nome legível humano do espaço de trabalho (aka "nome componente") | espaço de trabalho ("contosoretail") |
| Nome Qualificado | Nome completo do espaço de trabalho no formulário: "subscriçãoName/resourceGroup/componentName" | espaço de trabalho ('Contoso/ContosoRecurso/ContosoWorkspace') |
| ID | GUIA do espaço de trabalho | espaço de trabalho ("b438b3f6-912a-46d5-9db1-b42069242ab4") |
| ID de recursos azure | Identificador para o recurso Azure | espaço de trabalho ("/subscrições/e4227-645-44e-9c67-3b84b5982/resourcegroups/ContosoAzureHQ/providers/Microsoft.OperationalInsights/workspaces/contosoretail") |


## <a name="notes"></a>Notas

* Deve ter lido o acesso ao espaço de trabalho.
* Uma expressão `app` relacionada é que lhe permite consultar através de aplicações de Application Insights.

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

- Consulte a expressão da [aplicação](app-expression.md) para se referir a uma aplicação Application Insights.
- Leia sobre como os [dados do Monitor Azure](log-query-overview.md) são armazenados.
- Aceda à documentação completa para a [linguagem de consulta kusto.](/azure/kusto/query/)
