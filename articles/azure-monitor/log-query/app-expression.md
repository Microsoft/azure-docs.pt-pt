---
title: app() expressão em consultas de registo do Monitor Azure [ Microsoft Docs
description: A expressão da aplicação é usada numa consulta de registo do Azure Monitor para recuperar dados de uma aplicação específica da Application Insights no mesmo grupo de recursos, outro grupo de recursos ou outra subscrição.
ms.subservice: logs
ms.topic: conceptual
author: bwren
ms.author: bwren
ms.date: 05/09/2019
ms.openlocfilehash: 5d31c829487400f8eb239c0b837e53eecafeb900
ms.sourcegitcommit: a8ee9717531050115916dfe427f84bd531a92341
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 05/12/2020
ms.locfileid: "83201110"
---
# <a name="app-expression-in-azure-monitor-query"></a>app() expressão na consulta do Monitor Azure

A expressão é usada numa consulta do Azure Monitor para recuperar dados de uma aplicação específica do Application Insights no mesmo grupo de recursos, outro grupo de `app` recursos ou outra subscrição. Isto é útil para incluir dados de aplicação numa consulta de registo do Monitor Azure e para consultar dados em várias aplicações numa consulta de Insights de Aplicação.

> [!IMPORTANT]
> A expressão da aplicação() não é utilizada se estiver a utilizar um recurso de [Insights de aplicação baseado no espaço de trabalho,](../app/create-workspace-resource.md) uma vez que os dados de registo são armazenados num espaço de trabalho do Log Analytics. Utilize a expressão de registo para escrever uma consulta que inclua aplicação em vários espaços de trabalho. Para várias aplicações no mesmo espaço de trabalho, não precisa de uma consulta de espaço de trabalho transversal.

## <a name="syntax"></a>Sintaxe

`app(`*Identificador*`)`


## <a name="arguments"></a>Argumentos

- *Identificador*: Identifica a aplicação utilizando um dos formatos na tabela abaixo.

| Identificador | Descrição | Exemplo
|:---|:---|:---|
| Nome do Recurso | Nome legível humano da app (Também conhecido como "nome componente") | app ("fabrikamapp") |
| Nome Qualificado | Nome completo da aplicação no formulário: "subscriçãoName/resourceGroup/componentName" | app ('AI-Protótipo/Fabrikam/fabrikamapp') |
| ID | GUIA da app | app ("988ba129-363e-4415-8fe7-8cbab5447518") |
| ID de recursos azure | Identificador para o recurso Azure |app ("/subscrições/7293b69-db12-44fc-9a66-9c2005c3051d/resourcegroups/Fabrikam/providers/microsoft.insights/components/fabrikamapp") |


## <a name="notes"></a>Notas

* Deve ter lido o acesso à aplicação.
* Identificar uma aplicação pelo seu nome assume que é única em todas as subscrições acessíveis. Se tiver várias aplicações com o nome especificado, a consulta falhará devido à ambiguidade. Neste caso, deve utilizar um dos outros identificadores.
* Utilize o [espaço](workspace-expression.md) de trabalho de expressão relacionado para consultar os espaços de trabalho do Log Analytics.
* A expressão da aplicação() não é atualmente suportada na consulta de pesquisa ao utilizar o portal Azure para criar uma regra de alerta de pesquisa de [registo personalizado,](../platform/alerts-log.md)a menos que uma aplicação Application Insights seja usada como recurso para a regra de alerta.

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

- Consulte a [expressão espaço](workspace-expression.md) de trabalho para se referir a um espaço de trabalho do Log Analytics.
- Leia sobre como os [dados do Monitor Azure](../../azure-monitor/log-query/log-query-overview.md) são armazenados.
- Aceda à documentação completa para a [linguagem de consulta kusto.](/azure/kusto/query/)