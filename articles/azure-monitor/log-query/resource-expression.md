---
title: expressão de recurso () na consulta de log de Azure Monitor | Microsoft Docs
description: A expressão de recurso é usada em uma consulta de log de Azure Monitor centrada em recursos para recuperar dados de vários recursos.
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
ms.date: 09/10/2018
ms.author: bwren
ms.openlocfilehash: deca6e7ef1c231a82a73067971d86a6e9cdd0599
ms.sourcegitcommit: 80da36d4df7991628fd5a3df4b3aa92d55cc5ade
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/02/2019
ms.locfileid: "71817413"
---
# <a name="resource-expression-in-azure-monitor-log-query"></a>expressão de recurso () na consulta de log de Azure Monitor

A expressão `resource` é usada em uma consulta Azure Monitor com [escopo para um recurso](scope.md#query-scope) para recuperar dados de outros recursos. 


## <a name="syntax"></a>Sintaxe

*identificador*`resource(` `)`

## <a name="arguments"></a>Argumentos

- *Identificador*: ID de recurso de um recurso.

| Identificador | Descrição | Exemplo
|:---|:---|:---|
| Resource | Inclui dados para o recurso. | recurso ("/subscriptions/xxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx/resourcesgroups/myresourcegroup/Providers/Microsoft.Compute/VirtualMachines/MyVM") |
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
