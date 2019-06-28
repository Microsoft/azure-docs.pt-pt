---
title: Trabalhar com matrizes e objetos no Azure Cosmos DB
description: Saiba mais sobre a criação matriz e objeto sintaxe SQL do Azure Cosmos DB.
author: timsander1
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 06/21/2019
ms.author: tisande
ms.openlocfilehash: 338f3b51edf38d20a963992e121b7e2dbd0c6873
ms.sourcegitcommit: a12b2c2599134e32a910921861d4805e21320159
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 06/24/2019
ms.locfileid: "67342839"
---
# <a name="working-with-arrays-and-objects-in-azure-cosmos-db"></a>Trabalhar com matrizes e objetos no Azure Cosmos DB

Um recurso chave do Azure Cosmos DB SQL API é a criação de matriz e objeto.

## <a name="arrays"></a>matrizes

Pode construir matrizes, conforme mostrado no exemplo a seguir:

```sql
    SELECT [f.address.city, f.address.state] AS CityState
    FROM Families f
```

Os resultados são:

```json
    [
      {
        "CityState": [
          "Seattle",
          "WA"
        ]
      },
      {
        "CityState": [
          "NY", 
          "NY"
        ]
      }
    ]
```

Também pode utilizar o [expressão de matriz](sql-query-subquery.md#array-expression) construir uma matriz de [da subconsulta](sql-query-subquery.md) resultados. Esta consulta obtém todos os diferentes nomes de filhos numa matriz.

```sql
SELECT f.id, ARRAY(SELECT DISTINCT VALUE c.givenName FROM c IN f.children) as ChildNames
FROM f
```

## <a id="Iteration"></a>Iteração

A API de SQL fornece suporte para iterar através das matrizes JSON, com uma construção nova adicionada através da [palavra-chave](sql-query-keywords.md#in) na origem FROM. No exemplo a seguir:

```sql
    SELECT *
    FROM Families.children
```

Os resultados são:

```json
    [
      [
        {
          "firstName": "Henriette Thaulow",
          "gender": "female",
          "grade": 5,
          "pets": [{ "givenName": "Fluffy"}]
        }
      ], 
      [
        {
            "familyName": "Merriam",
            "givenName": "Jesse",
            "gender": "female",
            "grade": 1
        }, 
        {
            "familyName": "Miller",
            "givenName": "Lisa",
            "gender": "female",
            "grade": 8
        }
      ]
    ]
```

A próxima consulta faz a iteração através de `children` no `Families` contentor. A matriz de saída é diferente da consulta precedente. Neste exemplo divide `children`e nivela os resultados numa única matriz:  

```sql
    SELECT *
    FROM c IN Families.children
```

Os resultados são:

```json
    [
      {
          "firstName": "Henriette Thaulow",
          "gender": "female",
          "grade": 5,
          "pets": [{ "givenName": "Fluffy" }]
      },
      {
          "familyName": "Merriam",
          "givenName": "Jesse",
          "gender": "female",
          "grade": 1
      },
      {
          "familyName": "Miller",
          "givenName": "Lisa",
          "gender": "female",
          "grade": 8
      }
    ]
```

Pode filtrar ainda mais em cada entrada individual da matriz, como mostrado no exemplo a seguir:

```sql
    SELECT c.givenName
    FROM c IN Families.children
    WHERE c.grade = 8
```

Os resultados são:

```json
    [{
      "givenName": "Lisa"
    }]
```

Também pode agregar sobre o resultado de uma iteração de matriz. Por exemplo, a consulta seguinte conta o número de elementos subordinados entre todas as famílias:

```sql
    SELECT COUNT(child)
    FROM child IN Families.children
```

Os resultados são:

```json
    [
      {
        "$1": 3
      }
    ]
```

## <a name="next-steps"></a>Passos Seguintes

- [Introdução](sql-query-getting-started.md)
- [Exemplos do Azure Cosmos DB .NET](https://github.com/Azure/azure-cosmosdb-dotnet)
- [Associações](sql-query-join.md)