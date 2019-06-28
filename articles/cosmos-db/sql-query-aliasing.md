---
title: Aliasing no Azure Cosmos DB
description: Saiba mais sobre os valores de aliasing nas consultas de SQL do Azure Cosmos DB
author: markjbrown
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 06/20/2019
ms.author: mjbrown
ms.openlocfilehash: e532fb7180af8a21de6ae9a2e4d798abd9e93e7b
ms.sourcegitcommit: a12b2c2599134e32a910921861d4805e21320159
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 06/24/2019
ms.locfileid: "67342765"
---
# <a name="aliasing-in-azure-cosmos-db"></a>Aliasing no Azure Cosmos DB

Pode explicitamente alias valores nas consultas. Se uma consulta tem duas propriedades com o mesmo nome, utilize o aliasing para mudar o nome de uma ou ambas as propriedades para que esteja a ambigüidade removidas no resultado previsto.

## <a name="examples"></a>Exemplos

A palavra-chave usada para aliasing é opcional, conforme mostrado no exemplo a seguir ao projetar o segundo valor como `NameInfo`:

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

## <a name="next-steps"></a>Passos Seguintes

- [Exemplos do Azure Cosmos DB .NET](https://github.com/Azure/azure-cosmosdb-dotnet)
- [Cláusula SELECT](sql-query-select.md)
- [Cláusula FROM](sql-query-from.md)
