---
title: Trabalhando com matrizes e objetos em Azure Cosmos DB
description: Aprenda a sintaxe SQL para criar matrizes e objetos em Azure Cosmos DB. Este artigo também fornece alguns exemplos para executar operações em objetos de matriz
author: timsander1
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 12/02/2019
ms.author: tisande
ms.openlocfilehash: 2b882e1e39f035d27fc6d09d1a9d0c04691b499c
ms.sourcegitcommit: ac5cbef0706d9910a76e4c0841fdac3ef8ed2e82
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 09/03/2020
ms.locfileid: "89426253"
---
# <a name="working-with-arrays-and-objects-in-azure-cosmos-db"></a>Trabalhando com matrizes e objetos em Azure Cosmos DB

Uma característica chave do Azure Cosmos DB SQL API é a criação de matrizes e objetos.

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

Também pode utilizar a [expressão ARRAY](sql-query-subquery.md#array-expression) para construir uma matriz a partir dos resultados [do subquery.](sql-query-subquery.md) Esta consulta obtém todos os nomes distintos de crianças numa matriz.

```sql
SELECT f.id, ARRAY(SELECT DISTINCT VALUE c.givenName FROM c IN f.children) as ChildNames
FROM f
```

## <a name="iteration"></a><a id="Iteration"></a>Iteração

A API SQL fornece suporte para iterar sobre matrizes JSON, com uma nova construção adicionada através da [palavra-chave IN](sql-query-keywords.md#in) na fonte FROM. No seguinte exemplo:

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

A consulta seguinte executa a iteração `children` no `Families` recipiente. A matriz de saída é diferente da consulta anterior. Este exemplo divide-se `children` e achata os resultados numa única matriz:  

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

Pode filtrar ainda mais cada entrada individual da matriz, como mostra o seguinte exemplo:

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

Também pode agregar o resultado de uma iteração de matrizes. Por exemplo, a seguinte consulta conta o número de crianças entre todas as famílias:

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

## <a name="next-steps"></a>Próximos passos

- [Introdução](sql-query-getting-started.md)
- [Amostras de Azure Cosmos DB .NET](https://github.com/Azure/azure-cosmos-dotnet-v3)
- [Associações](sql-query-join.md)
