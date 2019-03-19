---
title: expressão de App() nas consultas de registo do Azure Monitor | Documentos da Microsoft
description: A expressão de aplicação é utilizada uma consulta de registo do Azure Monitor para recuperar dados de uma aplicação específica do Application Insights no mesmo grupo de recursos, outro grupo de recursos ou outra subscrição.
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
ms.date: 01/25/2019
ms.author: bwren
ms.openlocfilehash: a1a605bc733597430f64dceeb6c485db0abf657b
ms.sourcegitcommit: 5839af386c5a2ad46aaaeb90a13065ef94e61e74
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/19/2019
ms.locfileid: "57889459"
---
# <a name="app-expression-in-azure-monitor-query"></a>expressão de App() na consulta do Azure Monitor

O `app` expressão é usada numa consulta do Azure Monitor para recuperar dados de uma aplicação específica do Application Insights no mesmo grupo de recursos, outro grupo de recursos ou outra subscrição. Isto é útil para incluir dados de aplicativos numa consulta de registo do Azure Monitor e para consultar dados com os vários aplicativos numa consulta do Application Insights.



## <a name="syntax"></a>Sintaxe

`app(`*Identificador*`)`


## <a name="arguments"></a>Argumentos

- *Identificador*: Identifica a aplicação com um dos formatos na tabela abaixo.

| Identificador | Descrição | Exemplo
|:---|:---|:---|
| Nome do Recurso | Nome legível por humano da aplicação (também conhecidas como "nome do componente") | app("fabrikamapp") |
| Nome qualificado | Nome completo da aplicação no formato: "subscriptionName/resourceGroup/componentName" | app('AI-Prototype/Fabrikam/fabrikamapp') |
| ID | GUID da aplicação | app("988ba129-363e-4415-8fe7-8cbab5447518") |
| ID de recurso do Azure | Identificador para o recurso do Azure |app("/subscriptions/7293b69-db12-44fc-9a66-9c2005c3051d/resourcegroups/Fabrikam/providers/microsoft.insights/components/fabrikamapp") |


## <a name="notes"></a>Notas

* Tem de ter acesso de leitura para a aplicação.
* Identificar um aplicativo pelo respetivo nome de parte do princípio de que é exclusivo em todas as subscrições acessíveis. Se tiver várias aplicações com o nome especificado, a consulta irá falhar devido a ambiguidade. Neste caso tem de utilizar um dos outros identificadores.
* Utilizar a expressão relacionada [área de trabalho](workspace-expression.md) a consulta em áreas de trabalho do Log Analytics.
* A expressão de app() atualmente não é suportada na consulta de pesquisa quando utilizar o portal do Azure para criar uma [regra de alerta de pesquisa de registo personalizado](../platform/alerts-log.md), a menos que um aplicativo do Application Insights é utilizado como o recurso para a regra de alerta.

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

## <a name="next-steps"></a>Passos Seguintes

- Consulte a [expressão de área de trabalho](workspace-expression.md) para fazer referência a uma área de trabalho do Log Analytics.
- Saiba mais sobre como [dados do Azure Monitor](../../azure-monitor/log-query/log-query-overview.md) são armazenados.
- Aceder a documentação completa para o [linguagem de consulta de Kusto](/azure/kusto/query/).
