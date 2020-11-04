---
title: Palavras-chave SQL para Azure Cosmos DB
description: Saiba mais sobre as palavras-chave SQL para Azure Cosmos DB.
author: timsander1
ms.service: cosmos-db
ms.subservice: cosmosdb-sql
ms.topic: conceptual
ms.date: 07/29/2020
ms.author: tisande
ms.openlocfilehash: 35232f95bc18432db05775807d95f23ceab66aea
ms.sourcegitcommit: fa90cd55e341c8201e3789df4cd8bd6fe7c809a3
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 11/04/2020
ms.locfileid: "93333788"
---
# <a name="keywords-in-azure-cosmos-db"></a>Palavras-chave em Azure Cosmos DB
[!INCLUDE[appliesto-sql-api](includes/appliesto-sql-api.md)]

Este artigo detalha palavras-chave que podem ser usadas em consultas Azure Cosmos DB SQL.

## <a name="between"></a>BETWEEN

Pode utilizar a `BETWEEN` palavra-chave para expressar consultas contra gamas de cordas ou valores numéricos. Por exemplo, a seguinte consulta devolve todos os itens em que a primeira nota da criança é 1-5, inclusive.

```sql
    SELECT *
    FROM Families.children[0] c
    WHERE c.grade BETWEEN 1 AND 5
```

Também pode usar a `BETWEEN` palavra-chave na `SELECT` cláusula, como no exemplo a seguir.

```sql
    SELECT (c.grade BETWEEN 0 AND 10)
    FROM Families.children[0] c
```

Na API SQL, ao contrário da ANSI SQL, pode expressar consultas de alcance contra propriedades de tipos mistos. Por exemplo, `grade` pode ser um número como em `5` alguns itens e uma corda como `grade4` em outros. Nestes casos, como no JavaScript, a comparação entre os dois tipos diferentes resulta em `Undefined` , pelo que o item é ignorado.

## <a name="distinct"></a>DISTINCT

A `DISTINCT` palavra-chave elimina duplicados na projeção da consulta.

Neste exemplo, os valores de projetos de consulta para cada apelido:

```sql
SELECT DISTINCT VALUE f.lastName
FROM Families f
```

Os resultados são:

```json
[
    "Andersen"
]
```

Também pode projetar objetos únicos. Neste caso, o último campo de nome não existe num dos dois documentos, pelo que a consulta devolve um objeto vazio.

```sql
SELECT DISTINCT f.lastName
FROM Families f
```

Os resultados são:

```json
[
    {
        "lastName": "Andersen"
    },
    {}
]
```

`DISTINCT` pode também ser utilizado na projeção dentro de um subquery:

```sql
SELECT f.id, ARRAY(SELECT DISTINCT VALUE c.givenName FROM c IN f.children) as ChildNames
FROM f
```

Esta consulta projeta uma matriz que contém o nome dado de cada criança com duplicados removidos. Esta matriz é nomeadamente nomeada como ChildNames e projetada na consulta exterior.

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

As consultas com uma função de sistema agregada e um subquery com `DISTINCT` não são suportadas. Por exemplo, a seguinte consulta não é suportada:

```sql
SELECT COUNT(1) FROM (SELECT DISTINCT f.lastName FROM f)
```

## <a name="in"></a>IN

Utilize a palavra-chave IN para verificar se um valor especificado corresponde a qualquer valor numa lista. Por exemplo, a seguinte consulta devolve todos os itens familiares onde `id` o é `WakefieldFamily` ou `AndersenFamily` .

```sql
    SELECT *
    FROM Families
    WHERE Families.id IN ('AndersenFamily', 'WakefieldFamily')
```

O exemplo a seguir devolve todos os itens em que o estado é qualquer um dos valores especificados:

```sql
    SELECT *
    FROM Families
    WHERE Families.address.state IN ("NY", "WA", "CA", "PA", "OH", "OR", "MI", "WI", "MN", "FL")
```

A API SQL fornece suporte para [iterar sobre matrizes JSON,](sql-query-object-array.md#Iteration)com uma nova construção adicionada através da palavra-chave in na fonte FROM.

Se incluir a tecla de partição no `IN` filtro, a sua consulta filtrará automaticamente apenas as divisórias relevantes.

## <a name="top"></a>Início

A palavra-chave TOP devolve o primeiro `N` número de consultas resulta numa ordem indefinida. Como uma boa prática, use o TOP com a `ORDER BY` cláusula para limitar os resultados ao primeiro número de `N` valores ordenados. Combinar estas duas cláusulas é a única forma de indicar previsivelmente quais as linhas que o TOP afeta.

Pode utilizar o TOP com um valor constante, como no exemplo a seguir, ou com um valor variável utilizando consultas parametrizadas.

```sql
    SELECT TOP 1 *
    FROM Families f
```

Os resultados são:

```json
    [{
        "id": "AndersenFamily",
        "lastName": "Andersen",
        "parents": [
           { "firstName": "Thomas" },
           { "firstName": "Mary Kay"}
        ],
        "children": [
           {
               "firstName": "Henriette Thaulow", "gender": "female", "grade": 5,
               "pets": [{ "givenName": "Fluffy" }]
           }
        ],
        "address": { "state": "WA", "county": "King", "city": "Seattle" },
        "creationDate": 1431620472,
        "isRegistered": true
    }]
```

## <a name="next-steps"></a>Passos seguintes

- [Introdução](sql-query-getting-started.md)
- [Associações](sql-query-join.md)
- [Subconsultas](sql-query-subquery.md)