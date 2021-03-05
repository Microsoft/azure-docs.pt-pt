---
title: Palavras-chave SQL para Azure Cosmos DB
description: Saiba mais sobre as palavras-chave SQL para Azure Cosmos DB.
author: timsander1
ms.service: cosmos-db
ms.subservice: cosmosdb-sql
ms.topic: conceptual
ms.date: 01/20/2021
ms.author: tisande
ms.openlocfilehash: 1f3c4ef56feb77e9b01375b8b5dbdb567f5bfadb
ms.sourcegitcommit: 24a12d4692c4a4c97f6e31a5fbda971695c4cd68
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/05/2021
ms.locfileid: "102179974"
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

## <a name="like"></a>COMO

Devolve um valor Boolean dependendo se uma corda de carácter específico corresponde a um padrão especificado. Um padrão pode incluir caracteres regulares e caracteres wildcard. Pode escrever consultas logicamente equivalentes utilizando a `LIKE` palavra-chave ou a função do sistema [RegexMatch.](sql-query-regexmatch.md) Observará a mesma utilização do índice independentemente da escolha. Por isso, deve utilizar `LIKE` se preferir a sintaxe mais do que expressões regulares.

> [!NOTE]
> Porque `LIKE` pode utilizar um índice, deve criar um índice de [gama](./index-policy.md) para propriedades que está a comparar usando `LIKE` .

Pode utilizar os seguintes caracteres wildcard com LIKE:

| Personagem wildcard | Descrição                                                  | Exemplo                                     |
| -------------------- | ------------------------------------------------------------ | ------------------------------------------- |
| %                    | Qualquer cadeia de caracteres zero ou mais                      | ONDE c.description LIKE "%SO%PS%"      |
| _ (sublinhado)     | Qualquer personagem                                       | ONDE c.description LIKE "%SO_PS%"      |
| [ ]                  | Qualquer caracteres dentro do intervalo especificado ([a-f]) ou definido ([abcdef]). | ONDE c.description LIKE "%SO[t-z]PS%"  |
| [^]                  | Qualquer caracteres não dentro do intervalo especificado ([^a-f]) ou definido ([^abcdef]). | ONDE c.description LIKE "%SO[^abc]PS%" |


### <a name="using-like-with-the--wildcard-character"></a>Usando LIKE com o caráter % wildcard

O `%` personagem corresponde a qualquer sequência de zero ou mais caracteres. Por exemplo, colocando um `%` no início e no fim do padrão, a seguinte consulta devolve todos os itens com uma descrição que `fruit` contém:

```sql
SELECT *
FROM c
WHERE c.description LIKE "%fruit%"
```

Se usasse apenas um `%` personagem no final do padrão, só devolveria itens com uma descrição que começou `fruit` com:

```sql
SELECT *
FROM c
WHERE c.description LIKE "fruit%"
```


### <a name="using-not-like"></a>Usar NÃO GOSTO

O exemplo abaixo devolve todos os itens com uma descrição que não `fruit` contenha:

```sql
SELECT *
FROM c
WHERE c.description NOT LIKE "%fruit%"
```

### <a name="using-the-escape-clause"></a>Usando a cláusula de fuga

Pode pesquisar padrões que incluam um ou mais caracteres wildcard usando a cláusula ESCAPE. Por exemplo, se quisesse procurar descrições que contivessem a `20-30%` corda, não quereria interpretar o `%` carácter como um personagem wildcard.

```sql
SELECT *
FROM c
WHERE c.description LIKE '%20-30!%%' ESCAPE '!'
```

### <a name="using-wildcard-characters-as-literals"></a>Usando caracteres wildcard como literais

Você pode incluir caracteres wildcard em parênteses para tratá-los como caracteres literais. Quando se encerra um personagem wildcard nos parênteses, remove-se quaisquer atributos especiais. Eis alguns exemplos:

| Padrão           | Significado |
| ----------------- | ------- |
| COMO "20-30[%]" | 20-30%  |
| LIKE "[_]n"     | _n      |
| COMO "[[]"    | [       |
| COMO "]"        | ]       |

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
