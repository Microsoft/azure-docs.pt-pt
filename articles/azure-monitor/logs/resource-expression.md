---
title: () expressão na consulta de registo do Monitor Azure | Microsoft Docs
description: A expressão de recursos é usada numa consulta de registo do Monitor Azure centrado em recursos para obter dados de vários recursos.
ms.subservice: logs
ms.topic: conceptual
author: bwren
ms.author: bwren
ms.date: 09/10/2018
ms.openlocfilehash: 2a729caefe698b13833098ba48df9d4bfbd97356
ms.sourcegitcommit: e559daa1f7115d703bfa1b87da1cf267bf6ae9e8
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 02/17/2021
ms.locfileid: "100619521"
---
# <a name="resource-expression-in-azure-monitor-log-query"></a>() expressão na consulta de registo do Monitor de Azure

A `resource` expressão é utilizada numa consulta do Azure [Monitor, a um recurso para](scope.md#query-scope) obter dados de outros recursos. 


## <a name="syntax"></a>Syntax

`resource(`*Identificador*`)`

## <a name="arguments"></a>Argumentos

- *Identificador*: Identificação de recursos de um recurso.

| Identificador | Descrição | Exemplo
|:---|:---|:---|
| Recurso | Inclui dados para o recurso. | recurso ("/subscrições/xxxxxx-xxxx-xxxx-xxxx-xxxx/resourcesgroups/myresourcegroup/providers/microsoft.compute/virtualmachines/myvm") |
| Grupo de Recursos ou Subscrição | Inclui dados para o recurso e todos os recursos que contém.  | recurso ("/subscrições/xxxxxx-xxxx-xxxx-xxxx-xxxx/resourcesgroups/myresourcegroup) |


## <a name="notes"></a>Notas

* Deve ter lido o acesso ao recurso.


## <a name="examples"></a>Exemplos

```Kusto
union (Heartbeat),(resource("/subscriptions/xxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx/resourcesgroups/myresourcegroup/providers/microsoft.compute/virtualmachines/myvm").Heartbeat) | summarize count() by _ResourceId, TenantId
```
```Kusto
union (Heartbeat),(resource("/subscriptions/xxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx/resourcesgroups/myresourcegroup).Heartbeat) | summarize count() by _ResourceId, TenantId
```


## <a name="next-steps"></a>Passos seguintes

- Consulte [o âmbito de consulta de registo e o intervalo de tempo no Azure Monitor Log Analytics](scope.md) para obter mais detalhes sobre um âmbito de consulta.
- Aceda à documentação completa para a [língua de consulta kusto.](/azure/kusto/query/)
