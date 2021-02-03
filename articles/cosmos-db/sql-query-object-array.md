---
title: Trabalhando com matrizes e objetos em Azure Cosmos DB
description: Aprenda a sintaxe SQL para criar matrizes e objetos em Azure Cosmos DB. Este artigo também fornece alguns exemplos para executar operações em objetos de matriz
author: timsander1
ms.service: cosmos-db
ms.subservice: cosmosdb-sql
ms.topic: conceptual
ms.date: 02/02/2021
ms.author: tisande
ms.openlocfilehash: 1dccb8e51fbc578f8f218fe1582f95f7bcaf42d7
ms.sourcegitcommit: 740698a63c485390ebdd5e58bc41929ec0e4ed2d
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 02/03/2021
ms.locfileid: "99493793"
---
# <a name="working-with-arrays-and-objects-in-azure-cosmos-db"></a>Trabalhando com matrizes e objetos em Azure Cosmos DB
[!INCLUDE[appliesto-sql-api](includes/appliesto-sql-api.md)]

Uma característica chave do Azure Cosmos DB SQL API é a criação de matrizes e objetos. Este documento utiliza exemplos que podem ser recriados utilizando o [conjunto de dados da Família.](sql-query-getting-started.md#upload-sample-data)

Aqui está um item exemplo neste conjunto de dados:

```json
{
  "id": "AndersenFamily",
  "lastName": "Andersen",
  "parents": [
     { "firstName": "Thomas" },
     { "firstName": "Mary Kay"}
  ],
  "children": [
     {
         "firstName": "Henriette Thaulow",
         "gender": "female",
         "grade": 5,
         "pets": [{ "givenName": "Fluffy" }]
     }
  ],
  "address": { "state": "WA", "county": "King", "city": "Seattle" },
  "creationDate": 1431620472,
  "isRegistered": true
}
```

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

Os resultados são:

```json
[
    {
        "id": "AndersenFamily",
        "ChildNames": []
    },
    {
        "id": "WakefieldFamily",
        "ChildNames": [
            "Jesse",
            "Lisa"
        ]
    }
]
```

## <a name="iteration"></a><a id="Iteration"></a>Iteração

A API SQL fornece suporte para iterar sobre as matrizes JSON, com a [palavra-chave IN](sql-query-keywords.md#in) na fonte FROM. No seguinte exemplo:

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
SELECT COUNT(1) AS Count
FROM child IN Families.children
```

Os resultados são:

```json
[
  {
    "Count": 3
  }
]
```

> [!NOTE]
> Ao utilizar a palavra-chave IN para iteração, não é possível filtrar ou projetar quaisquer propriedades fora da matriz. Em vez disso, deve utilizar [JOINs](sql-query-join.md).

Para outros exemplos, leia a nossa [publicação de blog sobre como trabalhar com matrizes em Azure Cosmos DB](https://devblogs.microsoft.com/cosmosdb/understanding-how-to-query-arrays-in-azure-cosmos-db/).

## <a name="next-steps"></a>Passos seguintes

- [Introdução](sql-query-getting-started.md)
- [Amostras de Azure Cosmos DB .NET](https://github.com/Azure/azure-cosmos-dotnet-v3)
- [Associações](sql-query-join.md)
