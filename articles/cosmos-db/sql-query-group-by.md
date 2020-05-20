---
title: Cláusula GROUP BY em Azure Cosmos DB
description: Conheça a cláusula GROUP BY para o Azure Cosmos DB.
author: timsander1
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 05/19/2020
ms.author: tisande
ms.openlocfilehash: b602b56d37cec0e23d31318f6675d031bdd6bcdb
ms.sourcegitcommit: 595cde417684e3672e36f09fd4691fb6aa739733
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 05/20/2020
ms.locfileid: "83701001"
---
# <a name="group-by-clause-in-azure-cosmos-db"></a>Cláusula GROUP BY em Azure Cosmos DB

A cláusula GROUP BY divide os resultados da consulta de acordo com os valores de um ou mais imóveis especificados.

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
  
   Qualquer expressão escalar é permitida, exceto para subqueries escalar e agregados escalar. Cada expressão escalar deve conter pelo menos uma referência de propriedade. Não há limite para o número de expressões individuais ou para a cardinalidade de cada expressão.

## <a name="remarks"></a>Observações
  
  Quando uma consulta utiliza uma cláusula GROUP BY, a cláusula SELECT só pode conter o subconjunto de propriedades e funções do sistema incluídas na cláusula GROUP BY. Uma exceção são [as funções agregadas](sql-query-aggregates.md)do sistema, que podem aparecer na cláusula SELECT sem serem incluídas na cláusula GROUP BY. Também pode incluir sempre valores literais na cláusula SELECT.

  A cláusula GROUP BY deve ser posterior à cláusula SELECT, FROM e WHERE e antes da cláusula OFFSET LIMIT. Atualmente não pode utilizar o GROUP BY com uma cláusula ORDER BY, mas está previsto.

  A cláusula GROUP BY não permite qualquer das seguintes:
  
- Propriedades de aliasing ou funções do sistema de alisamento (o aliasing ainda é permitido dentro da cláusula SELECT)
- Subqueides
- Funções agregadas do sistema (estas são permitidas apenas na cláusula SELECT)

Consultas com uma função de sistema agregado e uma subqueria com `GROUP BY` não são suportadas. Por exemplo, a seguinte consulta não é suportada:

```sql
SELECT COUNT(UniqueLastNames)
FROM (
SELECT AVG(f.age)
FROM f
GROUP BY f.lastName
) AS UniqueLastNames
```

## <a name="examples"></a>Exemplos

Estes exemplos usam o conjunto de dados nutricionais disponíveis através do [Parque De Consulta DB Do MB Do Azure Cosmos.](https://www.documentdb.com/sql/demo)

Por exemplo, aqui está uma consulta que devolve a contagem total de itens em cada alimentoGroup:

```sql
SELECT TOP 4 COUNT(1) AS foodGroupCount, f.foodGroup
FROM Food f
GROUP BY f.foodGroup
```

Alguns resultados são (a palavra-chave TOP é usada para limitar resultados):

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

Esta consulta tem uma função de sistema na cláusula GROUP BY:

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
