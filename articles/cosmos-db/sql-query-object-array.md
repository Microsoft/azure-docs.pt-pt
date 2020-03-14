---
title: Trabalhando com matrizes e objetos em Azure Cosmos DB
description: Aprenda a sintaxe SQL para criar matrizes e objetos em Azure Cosmos DB. Este artigo também fornece alguns exemplos para realizar operações em objetos de matriz
author: timsander1
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 12/02/2019
ms.author: tisande
ms.openlocfilehash: 5b2801b0a71f04803955e9d8bc18a97133019996
ms.sourcegitcommit: 7b25c9981b52c385af77feb022825c1be6ff55bf
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/13/2020
ms.locfileid: "79246555"
---
# <a name="working-with-arrays-and-objects-in-azure-cosmos-db"></a>Trabalhando com matrizes e objetos em Azure Cosmos DB

Uma característica chave da API Azure Cosmos DB SQL é a criação de matriz e objeto.

## <a name="arrays"></a>Matrizes

Pode construir matrizes, como mostra o seguinte exemplo:

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

Também pode usar a [expressão ARRAY](sql-query-subquery.md#array-expression) para construir uma matriz a partir dos resultados [do subquery.](sql-query-subquery.md) Esta consulta recebe todos os nomes distintos de crianças numa matriz.

```sql
SELECT f.id, ARRAY(SELECT DISTINCT VALUE c.givenName FROM c IN f.children) as ChildNames
FROM f
```

## <a id="Iteration"></a>Iteração

A SQL API fornece suporte para iterar sobre matrizes JSON, com uma nova construção adicionada através da [palavra-chave IN](sql-query-keywords.md#in) na fonte FROM. No seguinte exemplo:

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

A próxima consulta executa a iteração sobre `children` no recipiente `Families`. A matriz de saída é diferente da consulta anterior. Este exemplo divide `children`e achata os resultados numa única matriz:  

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

Pode filtrar mais em cada entrada individual da matriz, como mostra o seguinte exemplo:

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

Também pode agregar-se sobre o resultado de uma iteração de matriz. Por exemplo, a seguinte consulta conta o número de crianças entre todas as famílias:

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

## <a name="next-steps"></a>Passos seguintes

- [Introdução](sql-query-getting-started.md)
- [Amostras Azure Cosmos DB .NET](https://github.com/Azure/azure-cosmos-dotnet-v3)
- [Junta-se](sql-query-join.md)