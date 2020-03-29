---
title: Consultas parametrizadas em Azure Cosmos DB
description: Saiba como as consultas parametrizadas SQL proporcionam um manuseamento robusto e fuga da entrada do utilizador e evite a exposição acidental de dados através da injeção de SQL.
author: timsander1
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 05/30/2019
ms.author: tisande
ms.openlocfilehash: e15a8236723c1efd80f27f2d253e9bbc44af4b0b
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/27/2020
ms.locfileid: "74870824"
---
# <a name="parameterized-queries-in-azure-cosmos-db"></a>Consultas parametrizadas em Azure Cosmos DB

Cosmos DB suporta consultas com parâmetros expressos pela notação familiar @ notation. O SQL parametrizado fornece um manuseamento robusto e escapada da entrada do utilizador, e evita a exposição acidental de dados através da injeção de SQL.

## <a name="examples"></a>Exemplos

Por exemplo, pode escrever uma `lastName` consulta `address.state` que leva e como parâmetros, e executá-la por vários valores de `lastName` e `address.state` com base na entrada do utilizador.

```sql
    SELECT *
    FROM Families f
    WHERE f.lastName = @lastName AND f.address.state = @addressState
```

Em seguida, pode enviar este pedido à Cosmos DB como uma consulta de JSON parametrizada como a seguinte:

```sql
    {
        "query": "SELECT * FROM Families f WHERE f.lastName = @lastName AND f.address.state = @addressState",
        "parameters": [
            {"name": "@lastName", "value": "Wakefield"},
            {"name": "@addressState", "value": "NY"},
        ]
    }
```

O exemplo que se segue define o argumento TOP com uma consulta parametrizada: 

```sql
    {
        "query": "SELECT TOP @n * FROM Families",
        "parameters": [
            {"name": "@n", "value": 10},
        ]
    }
```

Os valores dos parâmetros podem ser qualquer JSON válido: cordas, números, booleanos, matrizes nulos, parde matrizes ou JSON aninhado. Como cosmos DB é inútil, os parâmetros não são validados contra qualquer tipo.


## <a name="next-steps"></a>Passos seguintes

- [Amostras Azure Cosmos DB .NET](https://github.com/Azure/azure-cosmos-dotnet-v3)
- [Dados de documento do modelo](modeling-data.md)
