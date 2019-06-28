---
title: Consultas parametrizadas no Azure Cosmos DB
description: Saiba mais sobre consultas SQL parametrizado
author: timsander1
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 05/30/2019
ms.author: tisande
ms.openlocfilehash: 2bfc22346c1dd43d7d3c2937ffc286e48ae774d0
ms.sourcegitcommit: a12b2c2599134e32a910921861d4805e21320159
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 06/24/2019
ms.locfileid: "67342677"
---
# <a name="parameterized-queries-in-azure-cosmos-db"></a>Consultas parametrizadas no Azure Cosmos DB

O cosmos DB suporta consultas com parâmetros expressados por familiar @ notação. SQL parametrizado fornece processamento robusto e carateres de escape de entrada do usuário e impede a exposição acidental de dados por meio de injeção de SQL.

## <a name="examples"></a>Exemplos

Por exemplo, pode escrever uma consulta que demora `lastName` e `address.state` como parâmetros e executá-lo para vários valores de `lastName` e `address.state` com base na entrada do usuário.

```sql
    SELECT *
    FROM Families f
    WHERE f.lastName = @lastName AND f.address.state = @addressState
```

Em seguida, pode enviar este pedido para o Cosmos DB como uma consulta parametrizada de JSON semelhante ao seguinte:

```sql
    {
        "query": "SELECT * FROM Families f WHERE f.lastName = @lastName AND f.address.state = @addressState",
        "parameters": [
            {"name": "@lastName", "value": "Wakefield"},
            {"name": "@addressState", "value": "NY"},
        ]
    }
```

O exemplo seguinte define o argumento de principal com uma consulta parametrizada: 

```sql
    {
        "query": "SELECT TOP @n * FROM Families",
        "parameters": [
            {"name": "@n", "value": 10},
        ]
    }
```

Valores de parâmetro podem ser qualquer um JSON válido: cadeias, números, booleanos, nulos, até mesmo matrizes ou JSON de aninhados. Uma vez que o Cosmos DB não tem esquema, parâmetros não são validados em relação a qualquer tipo.


## <a name="next-steps"></a>Passos Seguintes

- [Exemplos do Azure Cosmos DB .NET](https://github.com/Azure/azure-cosmosdb-dotnet)
- [Dados de documento do modelo](modeling-data.md)
