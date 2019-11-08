---
title: Cláusula GROUP BY no Azure Cosmos DB
description: Saiba mais sobre a cláusula GROUP BY para Azure Cosmos DB.
author: timsander1
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 10/11/2019
ms.author: tisande
ms.openlocfilehash: e41e81457421bfe27e3c0313fc06e39e6df4cdce
ms.sourcegitcommit: ac56ef07d86328c40fed5b5792a6a02698926c2d
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 11/08/2019
ms.locfileid: "73819112"
---
# <a name="group-by-clause-in-azure-cosmos-db"></a>Cláusula GROUP BY no Azure Cosmos DB

A cláusula GROUP BY divide os resultados da consulta de acordo com os valores de uma ou mais propriedades especificadas.

> [!NOTE]
> O Azure Cosmos DB atualmente dá suporte a GROUP BY no SDK do .NET 3,3 e superior, bem como ao SDK do JavaScript 3,4 e superior.
> O suporte para outros SDK do idioma não está disponível no momento, mas está planejado.

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
  
   Qualquer expressão escalar é permitida, exceto para subconsultas escalares e agregações escalares. Cada expressão escalar deve conter pelo menos uma referência de propriedade. Não há nenhum limite para o número de expressões individuais ou a cardinalidade de cada expressão.

## <a name="remarks"></a>Observações
  
  Quando uma consulta usa uma cláusula GROUP BY, a cláusula SELECT pode conter apenas o subconjunto de propriedades e funções do sistema incluídas na cláusula GROUP BY. Uma exceção são [funções de sistema agregadas](sql-query-aggregates.md), que podem aparecer na cláusula SELECT sem serem incluídas na cláusula Group by. Você também pode incluir sempre valores literais na cláusula SELECT.

  A cláusula GROUP BY deve ser posterior à cláusula SELECT, FROM e WHERE e antes da cláusula de limite de deslocamento. No momento, você não pode usar GROUP BY com uma cláusula ORDER BY, mas isso é planejado.

  A cláusula GROUP BY não permite nenhum dos seguintes:
  
- Propriedades de alias ou funções do sistema de alias (a alias ainda é permitida dentro da cláusula SELECT)
- Subconsultas
- Funções de sistema agregadas (somente são permitidas na cláusula SELECT)

## <a name="examples"></a>Exemplos

Esses exemplos usam o conjunto de dados nutrição disponível por meio do [Playground para Consultas Azure Cosmos DB](https://www.documentdb.com/sql/demo).

Por exemplo, aqui está uma consulta que retorna a contagem total de itens em cada um dos alimentos:

```sql
SELECT TOP 4 COUNT(1) AS foodGroupCount, f.foodGroup
FROM Food f
GROUP BY f.foodGroup
```

Alguns resultados são (a palavra-chave TOP é usada para limitar os resultados):

```json
[{
  "foodGroup": "Fast Foods",
  "foodGroupCount": 371
},
{
  "foodGroup": "Finfish and Shellfish Products",
  "foodGroupCount": 267
},
{
  "foodGroup": "Meals, Entrees, and Side Dishes",
  "foodGroupCount": 113
},
{
  "foodGroup": "Sausages and Luncheon Meats",
  "foodGroupCount": 244
}]
```

Essa consulta tem duas expressões usadas para dividir os resultados:

```sql
SELECT TOP 4 COUNT(1) AS foodGroupCount, f.foodGroup, f.version
FROM Food f
GROUP BY f.foodGroup, f.version
```

Alguns resultados são:

```json
[{
  "version": 1,
  "foodGroup": "Nut and Seed Products",
  "foodGroupCount": 133
},
{
  "version": 1,
  "foodGroup": "Finfish and Shellfish Products",
  "foodGroupCount": 267
},
{
  "version": 1,
  "foodGroup": "Fast Foods",
  "foodGroupCount": 371
},
{
  "version": 1,
  "foodGroup": "Sausages and Luncheon Meats",
  "foodGroupCount": 244
}]
```

Esta consulta tem uma função de sistema na cláusula GROUP BY:

```sql
SELECT TOP 4 COUNT(1) AS foodGroupCount, UPPER(f.foodGroup) AS upperFoodGroup
FROM Food f
GROUP BY UPPER(f.foodGroup)
```

Alguns resultados são:

```json
[{
  "foodGroupCount": 371,
  "upperFoodGroup": "FAST FOODS"
},
{
  "foodGroupCount": 267,
  "upperFoodGroup": "FINFISH AND SHELLFISH PRODUCTS"
},
{
  "foodGroupCount": 389,
  "upperFoodGroup": "LEGUMES AND LEGUME PRODUCTS"
},
{
  "foodGroupCount": 113,
  "upperFoodGroup": "MEALS, ENTREES, AND SIDE DISHES"
}]
```

Essa consulta usa as duas palavras-chave e funções do sistema na expressão de Propriedade do item:

```sql
SELECT COUNT(1) AS foodGroupCount, ARRAY_CONTAINS(f.tags, {name: 'orange'}) AS containsOrangeTag,  f.version BETWEEN 0 AND 2 AS correctVersion
FROM Food f
GROUP BY ARRAY_CONTAINS(f.tags, {name: 'orange'}), f.version BETWEEN 0 AND 2
```

Os resultados são:

```json
[{
  "correctVersion": true,
  "containsOrangeTag": false,
  "foodGroupCount": 8608
},
{
  "correctVersion": true,
  "containsOrangeTag": true,
  "foodGroupCount": 10
}]
```

## <a name="next-steps"></a>Passos seguintes

- [Introdução](sql-query-getting-started.md)
- [Cláusula SELECT](sql-query-select.md)
- [Funções de agregação](sql-query-aggregates.md)
