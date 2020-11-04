---
title: GRUPO POR cláusula em Azure Cosmos DB
description: Conheça a cláusula GROUP BY para Azure Cosmos DB.
author: timsander1
ms.service: cosmos-db
ms.subservice: cosmosdb-sql
ms.topic: conceptual
ms.date: 05/19/2020
ms.author: tisande
ms.openlocfilehash: 71314e6999031f52ebbfc582b43684ab5a17a261
ms.sourcegitcommit: fa90cd55e341c8201e3789df4cd8bd6fe7c809a3
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 11/04/2020
ms.locfileid: "93338803"
---
# <a name="group-by-clause-in-azure-cosmos-db"></a>GRUPO POR cláusula em Azure Cosmos DB
[!INCLUDE[appliesto-sql-api](includes/appliesto-sql-api.md)]

O GRUPO POR cláusula divide os resultados da consulta de acordo com os valores de uma ou mais propriedades especificadas.

## <a name="syntax"></a>Sintaxe

```sql  
<group_by_clause> ::= GROUP BY <scalar_expression_list>

<scalar_expression_list> ::=
          <scalar_expression>
        | <scalar_expression_list>, <scalar_expression>
```  

## <a name="arguments"></a>Argumentos

- `<scalar_expression_list>`

   Especifica as expressões que serão usadas para dividir os resultados da consulta.

- `<scalar_expression>`
  
   Qualquer expressão escalar é permitida, exceto para subqueries escalar e agregados escalar. Cada expressão escalar deve conter pelo menos uma referência de propriedade. Não há limite para o número de expressões individuais ou para o cardinalício de cada expressão.

## <a name="remarks"></a>Observações
  
  Quando uma consulta utiliza uma cláusula GRUPO A, a cláusula SELECT só pode conter o subconjunto de propriedades e funções do sistema incluídas na cláusula GRUPO A. Uma exceção são [as funções agregadas](sql-query-aggregates.md)do sistema , que podem aparecer na cláusula SELECT sem serem incluídas na cláusula GROUP BY. Também pode sempre incluir valores literais na cláusula SELECT.

  A cláusula GRUPO A Cláusula deve ser após a cláusula SELECT, FROM e WHERE e antes da cláusula OFFSET LIMIT. Atualmente não pode utilizar o GROUP BY com uma cláusula ORDER BY, mas está previsto.

  O GRUPO POR cláusula não permite nenhum dos seguintes:
  
- Propriedades de aliasing ou funções do sistema de aliasing (o aliasing ainda é permitido dentro da cláusula SELECT)
- Subconsultas
- Funções agregadas do sistema (estas só são permitidas na cláusula SELECT)

As consultas com uma função de sistema agregada e um subquery com `GROUP BY` não são suportadas. Por exemplo, a seguinte consulta não é suportada:

```sql
SELECT COUNT(UniqueLastNames)
FROM (
SELECT AVG(f.age)
FROM f
GROUP BY f.lastName
) AS UniqueLastNames
```

## <a name="examples"></a>Exemplos

Estes exemplos utilizam o conjunto de dados nutricionais disponíveis através do [Azure Cosmos DB Query Playground](https://www.documentdb.com/sql/demo).

Por exemplo, aqui está uma consulta que devolve a contagem total de itens em cada grupo alimentar:

```sql
SELECT TOP 4 COUNT(1) AS foodGroupCount, f.foodGroup
FROM Food f
GROUP BY f.foodGroup
```

Alguns resultados são (a palavra-chave TOP é usada para limitar os resultados):

```json
[
    {
        "foodGroupCount": 183,
        "foodGroup": "Cereal Grains and Pasta"
    },
    {
        "foodGroupCount": 133,
        "foodGroup": "Nut and Seed Products"
    },
    {
        "foodGroupCount": 113,
        "foodGroup": "Meals, Entrees, and Side Dishes"
    },
    {
        "foodGroupCount": 64,
        "foodGroup": "Spices and Herbs"
    }
]
```

Esta consulta tem duas expressões usadas para dividir resultados:

```sql
SELECT TOP 4 COUNT(1) AS foodGroupCount, f.foodGroup, f.version
FROM Food f
GROUP BY f.foodGroup, f.version
```

Alguns resultados são:

```json
[
    {
        "foodGroupCount": 183,
        "foodGroup": "Cereal Grains and Pasta",
        "version": 1
    },
    {
        "foodGroupCount": 133,
        "foodGroup": "Nut and Seed Products",
        "version": 1
    },
    {
        "foodGroupCount": 113,
        "foodGroup": "Meals, Entrees, and Side Dishes",
        "version": 1
    },
    {
        "foodGroupCount": 64,
        "foodGroup": "Spices and Herbs",
        "version": 1
    }
]
```

Esta consulta tem uma função de sistema na cláusula GRUPO BY:

```sql
SELECT TOP 4 COUNT(1) AS foodGroupCount, UPPER(f.foodGroup) AS upperFoodGroup
FROM Food f
GROUP BY UPPER(f.foodGroup)
```

Alguns resultados são:

```json
[
    {
        "foodGroupCount": 183,
        "upperFoodGroup": "CEREAL GRAINS AND PASTA"
    },
    {
        "foodGroupCount": 133,
        "upperFoodGroup": "NUT AND SEED PRODUCTS"
    },
    {
        "foodGroupCount": 113,
        "upperFoodGroup": "MEALS, ENTREES, AND SIDE DISHES"
    },
    {
        "foodGroupCount": 64,
        "upperFoodGroup": "SPICES AND HERBS"
    }
]
```

Esta consulta utiliza palavras-chave e funções do sistema na expressão de propriedade do item:

```sql
SELECT COUNT(1) AS foodGroupCount, ARRAY_CONTAINS(f.tags, {name: 'orange'}) AS containsOrangeTag,  f.version BETWEEN 0 AND 2 AS correctVersion
FROM Food f
GROUP BY ARRAY_CONTAINS(f.tags, {name: 'orange'}), f.version BETWEEN 0 AND 2
```

Os resultados são:

```json
[
    {
        "foodGroupCount": 10,
        "containsOrangeTag": true,
        "correctVersion": true
    },
    {
        "foodGroupCount": 8608,
        "containsOrangeTag": false,
        "correctVersion": true
    }
]
```

## <a name="next-steps"></a>Passos seguintes

- [Introdução](sql-query-getting-started.md)
- [Cláusula SELECT](sql-query-select.md)
- [Funções agregadas](sql-query-aggregates.md)
