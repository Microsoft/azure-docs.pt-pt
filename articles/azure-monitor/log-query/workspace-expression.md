---
title: expressão de espaço de trabalho em consulta de log monitor Azure / Microsoft Docs
description: A expressão do espaço de trabalho é usada numa consulta de registo do Azure Monitor para obter dados de um espaço de trabalho específico no mesmo grupo de recursos, outro grupo de recursos ou outra subscrição.
ms.subservice: logs
ms.topic: conceptual
author: bwren
ms.author: bwren
ms.date: 09/10/2018
ms.openlocfilehash: 255888acf5da6149b6a964b23ed038b99715481c
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 07/02/2020
ms.locfileid: "75364956"
---
# <a name="workspace-expression-in-azure-monitor-log-query"></a>expressão de espaço de trabalho na consulta de log do Monitor de Azure

A `workspace` expressão é usada numa consulta do Azure Monitor para obter dados de um espaço de trabalho específico no mesmo grupo de recursos, outro grupo de recursos ou outra subscrição. Isto é útil para incluir dados de registo numa consulta de Insights de Aplicação e para consultar dados em vários espaços de trabalho numa consulta de log.


## <a name="syntax"></a>Syntax

`workspace(`*Identificador*`)`

## <a name="arguments"></a>Argumentos

- *Identificador*: Identifica o espaço de trabalho utilizando um dos formatos da tabela abaixo.

| Identificador | Descrição | Exemplo
|:---|:---|:---|
| Nome do Recurso | Nome legível humano do espaço de trabalho (aKA "nome componente") | espaço de trabalho ("contosoretail") |
| Nome Qualificado | Nome completo do espaço de trabalho no formulário: "nome de subscriçãoName/grupo de recursos/componenteName" | espaço de trabalho ('Contoso/ContosoResource/ContosoWorkspace') |
| ID | GUIADOr do espaço de trabalho | espaço de trabalho ("b438b3f6-912a-46d5-9db1-b42069242ab4") |
| ID de recursos Azure | Identificador para o recurso Azure | espaço de trabalho ("/subscrições/e4227-645-44e-9c67-3b84b5982/resourcegroups/ContosoAzureHQ/providers/Microsoft.OperationalInsights/workspaces/contosoretail") |


## <a name="notes"></a>Notas

* Deve ter lido o acesso ao espaço de trabalho.
* Uma expressão relacionada é `app` que permite consultar as aplicações Application Insights.

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

## <a name="next-steps"></a>Próximos passos

- Consulte a expressão da [aplicação](app-expression.md) para consultar uma aplicação Application Insights.
- Leia sobre como [os dados do Azure Monitor](log-query-overview.md) são armazenados.
- Aceda à documentação completa para a [língua de consulta kusto.](/azure/kusto/query/)
