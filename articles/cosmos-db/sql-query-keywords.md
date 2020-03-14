---
title: Palavras-chave SQL para Azure Cosmos DB
description: Saiba sobre as palavras-chave SQL para Azure Cosmos DB.
author: markjbrown
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 06/20/2019
ms.author: mjbrown
ms.openlocfilehash: 711e961bd5eb1607e2e6f11b0b5762423d78c0e7
ms.sourcegitcommit: 7b25c9981b52c385af77feb022825c1be6ff55bf
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/13/2020
ms.locfileid: "79246581"
---
# <a name="keywords-in-azure-cosmos-db"></a>Palavras-chave em Azure Cosmos DB
Este artigo detalha palavras-chave que podem ser usadas em consultas De SQL do Azure Cosmos DB.

## <a name="between"></a>ENTRE

Tal como no ANSI SQL, pode utilizar a palavra-chave ENTRE para expressar consultas contra gamas de cordas ou valores numéricos. Por exemplo, a seguinte consulta devolve todos os itens em que a nota da primeira criança é 1-5, inclusive.

```sql
    SELECT *
    FROM Families.children[0] c
    WHERE c.grade BETWEEN 1 AND 5
```

Ao contrário do QUE acontece no ANSI SQL, também pode utilizar a cláusula ENTRE na cláusula FROM, como no exemplo seguinte.

```sql
    SELECT (c.grade BETWEEN 0 AND 10)
    FROM Families.children[0] c
```

Na SQL API, ao contrário do ANSI SQL, pode expressar consultas de gama contra propriedades de tipos mistos. Por exemplo, `grade` pode ser um número como `5` em alguns itens e uma corda como `grade4` em outros. Nestes casos, tal como no JavaScript, a comparação entre os dois tipos diferentes resulta em `Undefined`, pelo que o item é ignorado.

> [!TIP]
> Para tempos de execução de consultas mais rápidas, crie uma política de indexação que utilize um tipo de índice de alcance contra quaisquer propriedades ou caminhos numéricos que a cláusula ENTRE filtra.

## <a name="distinct"></a>DISTINTO

A palavra-chave DISTINTA elimina duplicados na projeção da consulta.

Neste exemplo, os projetos de consulta valorizam para cada apelido:

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

Também pode projetar objetos únicos. Neste caso, o campo lastName não existe num dos dois documentos, pelo que a consulta devolve um objeto vazio.

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

Distinto também pode ser usado na projeção dentro de um subquery:

```sql
SELECT f.id, ARRAY(SELECT DISTINCT VALUE c.givenName FROM c IN f.children) as ChildNames
FROM f
```

Esta consulta projeta uma matriz que contém o nome dado de cada criança com duplicados removidos. Esta matriz é pseudónimo como ChildNames e projetada na consulta exterior.

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

Não são suportadas consultas com uma função de sistema agregado e uma subqueria com DISTINCT. Por exemplo, a seguinte consulta não é suportada:

```sql
SELECT COUNT(1) FROM (SELECT DISTINCT f.lastName FROM f)
```

## <a name="in"></a>IN

Utilize a palavra-chave IN para verificar se um valor especificado corresponde a qualquer valor numa lista. Por exemplo, a seguinte consulta devolve todos os itens familiares onde o `id` é `WakefieldFamily` ou `AndersenFamily`.

```sql
    SELECT *
    FROM Families
    WHERE Families.id IN ('AndersenFamily', 'WakefieldFamily')
```

O exemplo seguinte devolve todos os itens em que o estado é um dos valores especificados:

```sql
    SELECT *
    FROM Families
    WHERE Families.address.state IN ("NY", "WA", "CA", "PA", "OH", "OR", "MI", "WI", "MN", "FL")
```

A SQL API fornece suporte para [iterar sobre matrizes JSON,](sql-query-object-array.md#Iteration)com uma nova construção adicionada através da palavra-chave na fonte DA.

Se incluir a chave de partição no filtro `IN`, a sua consulta filtrará automaticamente apenas as divisórias relevantes.

## <a name="top"></a>Início

A palavra-chave TOP devolve o primeiro `N` número de consultas resulta numa ordem indefinida. Como uma boa prática, utilize top com a cláusula ORDER BY para limitar os resultados ao primeiro `N` número de valores encomendados. Combinar estas duas cláusulas é a única forma de indicar previsivelmente quais as linhas que o TOP afeta.

Pode utilizar top com um valor constante, como no exemplo seguinte, ou com um valor variável usando consultas parametrizadas.

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
- [Junta-se](sql-query-join.md)
- [Subqueides](sql-query-subquery.md)