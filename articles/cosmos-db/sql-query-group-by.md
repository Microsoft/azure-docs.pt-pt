---
title: Cláusula GROUP BY em Azure Cosmos DB
description: Conheça a cláusula GROUP BY para o Azure Cosmos DB.
author: timsander1
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 04/10/2020
ms.author: tisande
ms.openlocfilehash: 8a3cbbafc066747b62f79934f2cd12301aa1ba17
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/28/2020
ms.locfileid: "81261606"
---
# <a name="group-by-clause-in-azure-cosmos-db"></a>Cláusula GROUP BY em Azure Cosmos DB

A cláusula GROUP BY divide os resultados da consulta de acordo com os valores de um ou mais imóveis especificados.

> [!NOTE]
> Atualmente, o Azure Cosmos DB suporta o GROUP BY em .NET SDK 3.3 ou superior, bem como o JavaScript SDK 3.4 ou superior.
> O suporte para outras línguas SDK's não está disponível atualmente, mas está previsto.

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

Consultas com uma função de sistema `GROUP BY` agregado e uma subqueria com não são suportadas. Por exemplo, a seguinte consulta não é suportada:

```sql
SELECT COUNT(UniqueLastNames) FROM (SELECT AVG(f.age) FROM f GROUP BY f.lastName) AS UniqueLastNames
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

Esta consulta tem duas expressões usadas para dividir resultados:

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

Esta consulta utiliza palavras-chave e funções do sistema na expressão de propriedade do item:

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
- [Funções agregadas](sql-query-aggregates.md)
