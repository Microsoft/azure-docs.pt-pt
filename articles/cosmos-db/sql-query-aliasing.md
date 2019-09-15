---
title: Alias no Azure Cosmos DB
description: Saiba mais sobre valores de alias em consultas do Azure Cosmos DB SQL
author: markjbrown
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 06/20/2019
ms.author: mjbrown
ms.openlocfilehash: 3b17cbc7710647b1e1875025a1db1849034ec1dc
ms.sourcegitcommit: e97a0b4ffcb529691942fc75e7de919bc02b06ff
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 09/15/2019
ms.locfileid: "71002076"
---
# <a name="aliasing-in-azure-cosmos-db"></a>Alias no Azure Cosmos DB

Você pode explicitamente alias de valores em consultas. Se uma consulta tiver duas propriedades com o mesmo nome, use alias para renomear uma ou ambas as propriedades para que elas sejam desambiguadas no resultado projetado.

## <a name="examples"></a>Exemplos

A palavra-chave as usada para alias é opcional, conforme mostrado no exemplo a seguir ao projetar o segundo valor como `NameInfo`:

```sql
    SELECT 
           { "state": f.address.state, "city": f.address.city } AS AddressInfo,
           { "name": f.id } NameInfo
    FROM Families f
    WHERE f.id = "AndersenFamily"
```

Os resultados são:

```json
    [{
      "AddressInfo": {
        "state": "WA",
        "city": "Seattle"
      },
      "NameInfo": {
        "name": "AndersenFamily"
      }
    }]
```

## <a name="next-steps"></a>Passos seguintes

- [Exemplos do Azure Cosmos DB .NET](https://github.com/Azure/azure-cosmos-dotnet-v3)
- [Cláusula SELECT](sql-query-select.md)
- [Cláusula FROM](sql-query-from.md)
