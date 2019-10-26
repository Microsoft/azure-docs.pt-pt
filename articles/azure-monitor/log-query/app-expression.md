---
title: expressão de App () em consultas de log de Azure Monitor | Microsoft Docs
description: A expressão do aplicativo é usada em uma consulta Azure Monitor log para recuperar dados de um aplicativo Application Insights específico no mesmo grupo de recursos, em outro grupo de recursos ou em outra assinatura.
ms.service: azure-monitor
ms.subservice: logs
ms.topic: conceptual
author: bwren
ms.author: bwren
ms.date: 01/25/2019
ms.openlocfilehash: ffef73f88c8679d0b0be81222b1b61c4eaef5098
ms.sourcegitcommit: 4c3d6c2657ae714f4a042f2c078cf1b0ad20b3a4
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/25/2019
ms.locfileid: "72933087"
---
# <a name="app-expression-in-azure-monitor-query"></a>expressão de aplicativo () na consulta Azure Monitor

A expressão `app` é usada em uma consulta Azure Monitor para recuperar dados de um aplicativo Application Insights específico no mesmo grupo de recursos, outro grupo de recursos ou outra assinatura. Isso é útil para incluir dados de aplicativo em uma Azure Monitor consulta de log e consultar dados entre vários aplicativos em uma consulta Application Insights.



## <a name="syntax"></a>Sintaxe

*identificador* de `app(``)`


## <a name="arguments"></a>Argumentos

- *Identificador*: identifica o aplicativo usando um dos formatos na tabela a seguir.

| Identificador | Descrição | Exemplo
|:---|:---|:---|
| Nome do Recurso | Nome legível por humanos do aplicativo (também conhecido como "nome do componente") | aplicativo ("fabrikamapp") |
| Nome qualificado | Nome completo do aplicativo no formato: "subscriptionname/resourcegroup/ComponentName" | aplicativo (' AI-Prototype/Fabrikam/fabrikamapp ') |
| ID | GUID do aplicativo | aplicativo ("988ba129-363e-4415-8fe7-8cbab5447518") |
| ID de recurso do Azure | Identificador para o recurso do Azure |aplicativo ("/subscriptions/7293b69-db12-44fc-9a66-9c2005c3051d/resourcegroups/Fabrikam/providers/microsoft.insights/components/fabrikamapp") |


## <a name="notes"></a>Notas

* Você deve ter acesso de leitura ao aplicativo.
* A identificação de um aplicativo por seu nome pressupõe que ele seja exclusivo em todas as assinaturas acessíveis. Se você tiver vários aplicativos com o nome especificado, a consulta falhará devido à ambiguidade. Nesse caso, você deve usar um dos outros identificadores.
* Use o espaço de [trabalho](workspace-expression.md) de expressão relacionada para consultar em log Analytics espaços de trabalho.
* A expressão de aplicativo () atualmente não tem suporte na consulta de pesquisa ao usar o portal do Azure para criar uma [regra de alerta de pesquisa de logs personalizada](../platform/alerts-log.md), a menos que um aplicativo de Application insights seja usado como o recurso para a regra de alerta.

## <a name="examples"></a>Exemplos

```Kusto
app("fabrikamapp").requests | count
```
```Kusto
app("AI-Prototype/Fabrikam/fabrikamapp").requests | count
```
```Kusto
app("b438b4f6-912a-46d5-9cb1-b44069212ab4").requests | count
```
```Kusto
app("/subscriptions/7293b69-db12-44fc-9a66-9c2005c3051d/resourcegroups/Fabrikam/providers/microsoft.insights/components/fabrikamapp").requests | count
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

- Consulte a [expressão de espaço de trabalho](workspace-expression.md) para se referir a um espaço de trabalho log Analytics.
- Leia sobre como [Azure monitor dados](../../azure-monitor/log-query/log-query-overview.md) são armazenados.
- Acesse a documentação completa para a [linguagem de consulta Kusto](/azure/kusto/query/).
