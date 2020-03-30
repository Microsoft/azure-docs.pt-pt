---
title: expressão de recursos na consulta de registo do Monitor Azure ; Microsoft Docs
description: A expressão de recursos é usada numa consulta de registo do Azure Monitor centrada em recursos para recuperar dados de vários recursos.
ms.subservice: logs
ms.topic: conceptual
author: bwren
ms.author: bwren
ms.date: 09/10/2018
ms.openlocfilehash: 2a729caefe698b13833098ba48df9d4bfbd97356
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/28/2020
ms.locfileid: "77665704"
---
# <a name="resource-expression-in-azure-monitor-log-query"></a>expressão de recursos na consulta de registo do Monitor Azure

A `resource` expressão é usada numa consulta do Monitor [Azure, com recurso](scope.md#query-scope) para recolher dados de outros recursos. 


## <a name="syntax"></a>Sintaxe

`resource(`*Identificador*`)`

## <a name="arguments"></a>Argumentos

- *Identificador*: Identificação de recurso de um recurso.

| Identificador | Descrição | Exemplo
|:---|:---|:---|
| Recurso | Inclui dados para o recurso. | recursos ("/subscrições/xxxxxxx-xxxx-xxxx-xxxxxxxxxxxx/resourcesgroups/myresourcegroup/providers/microsoft.compute/virtualmachines/myvm") |
| Grupo de Recursos ou Subscrição | Inclui dados para o recurso e todos os recursos que contém.  | recurso ("/subscrições/xxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx/grupos de recursos/myresourcegroup) |


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

- Consulte o âmbito de consulta de registo e o intervalo de [tempo no Azure Monitor Log Analytics](scope.md) para obter detalhes sobre um âmbito de consulta.
- Aceda à documentação completa para a [linguagem de consulta kusto.](/azure/kusto/query/)
