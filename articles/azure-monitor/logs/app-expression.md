---
title: () expressão em Azure Monitor consultas de registo | Microsoft Docs
description: A expressão da aplicação é usada numa consulta de registo do Azure Monitor para obter dados de uma aplicação específica de Insights de Aplicação no mesmo grupo de recursos, outro grupo de recursos ou outra subscrição.
ms.subservice: logs
ms.topic: conceptual
author: bwren
ms.author: bwren
ms.date: 05/09/2019
ms.openlocfilehash: 4789594cef0f7e900582dd9aad231b81547d3b3e
ms.sourcegitcommit: c27a20b278f2ac758447418ea4c8c61e27927d6a
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/03/2021
ms.locfileid: "101728421"
---
# <a name="app-expression-in-azure-monitor-query"></a>() expressão na consulta do Monitor de Azure

A `app` expressão é usada numa consulta do Azure Monitor para obter dados de uma aplicação específica de Insights de Aplicação no mesmo grupo de recursos, outro grupo de recursos ou outra subscrição. Isto é útil para incluir dados de aplicações numa consulta de registo do Azure Monitor e para consultar dados em várias aplicações numa consulta de Insights de Aplicação.

> [!IMPORTANT]
> A expressão da aplicação não é utilizada se estiver a utilizar um [recurso de Insights de Aplicação baseado no espaço de trabalho,](../app/create-workspace-resource.md) uma vez que os dados de registo são armazenados num espaço de trabalho do Log Analytics. Utilize a expressão log() para escrever uma consulta que inclua aplicação em vários espaços de trabalho. Para várias aplicações no mesmo espaço de trabalho, você não precisa de uma consulta de espaço de trabalho transversal.

## <a name="syntax"></a>Syntax

`app(`*Identificador*`)`


## <a name="arguments"></a>Argumentos

- *Identificador*: Identifica a aplicação utilizando um dos formatos da tabela abaixo.

| Identificador | Descrição | Exemplo
|:---|:---|:---|
| Nome do Recurso | Nome legível humano da app (Também conhecido como "nome de componente") | app ("fabrikamapp") |
| Nome Qualificado | Nome completo da aplicação no formulário: "subscriptionName/resourceGroup/componentName" | app ('AI-Protótipo/Fabrikam/fabrikamapp') |
| ID | GUIA da app | app("988ba129-363e-4415-8fe7-8cbab5447518") |
| ID de recursos Azure | Identificador para o recurso Azure |app ("/subscrições/7293b69-db12-44fc-9a66-9c2005c3051d/resourcegroups/Fabrikam/providers/microsoft.insights/components/fabrikamapp") |


## <a name="notes"></a>Notas

* Deve ter lido o acesso à aplicação.
* Identificar uma aplicação pelo seu nome pressupõe que é única em todas as subscrições acessíveis. Se tiver várias aplicações com o nome especificado, a consulta falhará devido à ambiguidade. Neste caso, deve usar um dos outros identificadores.
* Use o [espaço de trabalho](../logs/workspace-expression.md) de expressão relacionada para consultar os espaços de trabalho do Log Analytics.
* A expressão da aplicação não é suportada na consulta de pesquisa quando utiliza o portal Azure para criar uma [regra de alerta de registo personalizado](../alerts/alerts-log.md), a menos que uma aplicação Application Insights seja usada como recurso para a regra de alerta.

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

- Consulte a [expressão do espaço de trabalho](../logs/workspace-expression.md) para se referir a um espaço de trabalho Log Analytics.
- Leia sobre como [os dados do Azure Monitor](./log-query-overview.md) são armazenados.
- Aceda à documentação completa para a [língua de consulta kusto.](/azure/kusto/query/)