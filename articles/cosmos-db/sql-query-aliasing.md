---
title: Aliasing em Azure Cosmos DB
description: Aprenda a usar o pseudónimo em consultas Azure Cosmos DB SQL para diferenciar duas propriedades com o mesmo nome
author: markjbrown
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 12/02/2019
ms.author: mjbrown
ms.openlocfilehash: 74849eec4c5808a584894321269c49c41f0b8a5c
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/28/2020
ms.locfileid: "74873476"
---
# <a name="aliasing-in-azure-cosmos-db"></a>Aliasing em Azure Cosmos DB

Pode explicitamente alias valores em consultas. Se uma consulta tiver duas propriedades com o mesmo nome, use o pseudónimo para mudar o nome de uma ou ambas as propriedades para que sejam desambiguadas no resultado projetado.

## <a name="examples"></a>Exemplos

A palavra-chave AS utilizada para aliasing é opcional, como mostra o `NameInfo`seguinte exemplo ao projetar o segundo valor como:

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

- [Amostras Azure Cosmos DB .NET](https://github.com/Azure/azure-cosmos-dotnet-v3)
- [Cláusula SELECT](sql-query-select.md)
- [Cláusula FROM](sql-query-from.md)
