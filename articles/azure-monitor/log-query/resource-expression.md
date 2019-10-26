---
title: expressão de recurso () na consulta de log de Azure Monitor | Microsoft Docs
description: A expressão de recurso é usada em uma consulta de log de Azure Monitor centrada em recursos para recuperar dados de vários recursos.
ms.service: azure-monitor
ms.subservice: logs
ms.topic: article
author: bwren
ms.author: bwren
ms.date: 09/10/2018
ms.openlocfilehash: 4787dc8a8cc06e0ad0be88597dab3481284fb58b
ms.sourcegitcommit: 5acd8f33a5adce3f5ded20dff2a7a48a07be8672
ms.translationtype: HT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/24/2019
ms.locfileid: "72900218"
---
# <a name="resource-expression-in-azure-monitor-log-query"></a>expressão de recurso () na consulta de log de Azure Monitor

A expressão de `resource` é usada em uma consulta Azure Monitor com [escopo para um recurso](scope.md#query-scope) para recuperar dados de outros recursos. 


## <a name="syntax"></a>Sintaxe

*identificador* de `resource(``)`

## <a name="arguments"></a>Argumentos

- *Identificador*: ID de recurso de um recurso.

| Identificador | Descrição | Exemplo
|:---|:---|:---|
| Recurso | Inclui dados para o recurso. | recurso ("/subscriptions/xxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx/resourcesgroups/myresourcegroup/Providers/Microsoft.Compute/VirtualMachines/MyVM") |
| Grupo de recursos ou assinatura | Inclui dados para o recurso e todos os recursos que ele contém.  | recurso ("/subscriptions/xxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx/resourcesgroups/myresourcegroup) |


## <a name="notes"></a>Notas

* Você deve ter acesso de leitura ao recurso.


## <a name="examples"></a>Exemplos

```Kusto
union (Heartbeat),(resource("/subscriptions/xxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx/resourcesgroups/myresourcegroup/providers/microsoft.compute/virtualmachines/myvm").Heartbeat) | summarize count() by _ResourceId, TenantId
```
```Kusto
union (Heartbeat),(resource("/subscriptions/xxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx/resourcesgroups/myresourcegroup).Heartbeat) | summarize count() by _ResourceId, TenantId
```


## <a name="next-steps"></a>Passos seguintes

- Consulte [escopo de consulta de log e intervalo de tempo em Azure Monitor log Analytics](scope.md) para obter detalhes sobre um escopo de consulta.
- Acesse a documentação completa para a [linguagem de consulta Kusto](/azure/kusto/query/).
