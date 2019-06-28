---
title: Palavras-chave SQL para o Azure Cosmos DB
description: Saiba mais sobre palavras-chave SQL para o Azure Cosmos DB.
author: markjbrown
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 06/20/2019
ms.author: mjbrown
ms.openlocfilehash: c9024f120e0a55162a1f6dba0cd9cbda97f5eebc
ms.sourcegitcommit: a12b2c2599134e32a910921861d4805e21320159
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 06/24/2019
ms.locfileid: "67342622"
---
# <a name="keywords-in-azure-cosmos-db"></a>Palavras-chave no Azure Cosmos DB
Este artigo detalha as palavras-chave que podem ser usadas em consultas de SQL do Azure Cosmos DB.

## <a name="between"></a>ENTRE

Como em ANSI SQL, pode usar a palavra-chave BETWEEN para expressar consultas em intervalos de valores numéricos ou de cadeia de caracteres. Por exemplo, a seguinte consulta devolve todos os itens em que nível o primeiro subordinado é de 1 a 5, inclusive.

```sql
    SELECT *
    FROM Families.children[0] c
    WHERE c.grade BETWEEN 1 AND 5
```

Ao contrário em ANSI SQL, também pode utilizar a cláusula BETWEEN na cláusula FROM, como no exemplo seguinte.

```sql
    SELECT (c.grade BETWEEN 0 AND 10)
    FROM Families.children[0] c
```

Na API de SQL, ao contrário da ANSI SQL, pode expressar consultas de intervalo em Propriedades de tipos mistos. Por exemplo, `grade` pode ser um número semelhante `5` em alguns itens e uma cadeia de caracteres como `grade4` em outros. Nestes casos, como em JavaScript, a comparação entre os dois tipos diferentes resulta em `Undefined`, por isso, o item é ignorado.

> [!TIP]
> Para tempos de execução de consulta mais rápidos, crie uma política de indexação que utiliza um tipo de índice do intervalo em relação a quaisquer propriedades numéricas ou caminhos que filtra a cláusula BETWEEN.

## <a name="distinct"></a>DISTINTOS

A palavra-chave DISTINCT elimina duplicados na projeção da consulta.

```sql
SELECT DISTINCT VALUE f.lastName
FROM Families f
```

Neste exemplo, a consulta projeta valores para cada apelido.

Os resultados são:

```json
[
    "Andersen"
]
```

Pode também projetar objetos exclusivos. Neste caso, o campo lastName não existe em um dos dois documentos, para que a consulta retorna um objeto vazio.

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

DISTINCT também pode ser usado na projecção dentro de uma subconsulta:

```sql
SELECT f.id, ARRAY(SELECT DISTINCT VALUE c.givenName FROM c IN f.children) as ChildNames
FROM f
```

Esta consulta projeta uma matriz que contém o givenName cada filho com os duplicados removidos. Essa matriz tiver o alias ChildNames e previsto na consulta externa.

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
## <a name="in"></a> IN

Utilize a palavra-chave IN para verificar se um valor especificado corresponde a um valor numa lista. Por exemplo, a seguinte consulta devolve todos os itens de famílias em que o `id` é `WakefieldFamily` ou `AndersenFamily`.

```sql
    SELECT *
    FROM Families
    WHERE Families.id IN ('AndersenFamily', 'WakefieldFamily')
```

O exemplo seguinte devolve todos os itens em que o estado é qualquer um dos valores especificados:

```sql
    SELECT *
    FROM Families
    WHERE Families.address.state IN ("NY", "WA", "CA", "PA", "OH", "OR", "MI", "WI", "MN", "FL")
```

A API de SQL fornece suporte para [iterar sobre matrizes JSON](sql-query-object-array.md#Iteration), com uma construção nova adicionada através da palavra-chave in na origem de FROM. 

## <a name="top"></a>PARTE SUPERIOR

A palavra-chave superior devolve o primeiro `N` o número de resultados da consulta numa ordem indefinido. Como melhor prática, utilize superior com a cláusula ORDER BY para limitar os resultados para o primeiro `N` o número de valores ordenadas. Combinar essas duas cláusulas é a única forma de forma previsível indicar que linhas afeta superior.

Pode usar a parte superior com um valor constante, como no exemplo seguinte, ou com um valor de variável usando consultas parametrizadas.

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

## <a name="next-steps"></a>Passos Seguintes

- [Introdução](sql-query-getting-started.md)
- [Associações](sql-query-join.md)
- [Subconsultas](sql-query-subquery.md)