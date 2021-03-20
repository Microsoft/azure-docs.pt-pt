---
title: Cláusula SELECT em Azure Cosmos DB
description: Saiba mais sobre a cláusula SQL SELECT para Azure Cosmos DB. Use o SQL como uma linguagem de consulta Azure Cosmos DB JSON.
author: timsander1
ms.service: cosmos-db
ms.subservice: cosmosdb-sql
ms.topic: conceptual
ms.date: 05/08/2020
ms.author: tisande
ms.openlocfilehash: 072e17b1c0ea312b4adfa1687e447fd2cadde233
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/19/2021
ms.locfileid: "93335450"
---
# <a name="select-clause-in-azure-cosmos-db"></a>Cláusula SELECT em Azure Cosmos DB
[!INCLUDE[appliesto-sql-api](includes/appliesto-sql-api.md)]

Cada consulta consiste numa `SELECT` cláusula e cláusulas opcionais [de E](sql-query-from.md) [ONDE,](sql-query-where.md) de acordo com os padrões ANSI SQL. Tipicamente, a fonte na `FROM` cláusula é enumerada, e a `WHERE` cláusula aplica um filtro na fonte para recuperar um subconjunto de itens JSON. A `SELECT` cláusula projeta então os valores JSON solicitados na lista selecionada.

## <a name="syntax"></a>Sintaxe

```sql
SELECT <select_specification>  

<select_specification> ::=
      '*'
      | [DISTINCT] <object_property_list>
      | [DISTINCT] VALUE <scalar_expression> [[ AS ] value_alias]  
  
<object_property_list> ::=
{ <scalar_expression> [ [ AS ] property_alias ] } [ ,...n ]  
```  
  
## <a name="arguments"></a>Argumentos
  
- `<select_specification>`  

  Propriedades ou valor a selecionar para o conjunto de resultados.  
  
- `'*'`  

  Especifica que o valor deve ser recuperado sem esprodução de quaisquer alterações. Especificamente se o valor processado é um objeto, todas as propriedades serão recuperadas.  
  
- `<object_property_list>`  
  
  Especifica a lista de propriedades a recuperar. Cada valor devolvido será um objeto com as propriedades especificadas.  
  
- `VALUE`  

  Especifica que o valor JSON deve ser recuperado em vez do objeto JSON completo. Isto, ao contrário `<property_list>` de não embrulhar o valor projetado num objeto.  

- `DISTINCT`
  
  Especifica que devem ser removidos duplicados de propriedades projetadas.  

- `<scalar_expression>`  

  Expressão que representa o valor a calcular. Consulte a secção [de expressões Scalar](sql-query-scalar-expressions.md) para obter mais detalhes.  

## <a name="remarks"></a>Observações

A `SELECT *` sintaxe só é válida se a cláusula FROM tiver declarado exatamente um pseudónimo. `SELECT *` fornece uma projeção de identidade, que pode ser útil se não for necessária nenhuma projeção. SELECT * só é válido se a cláusula FROM for especificada e introduzida apenas uma única fonte de entrada.  
  
Ambos `SELECT <select_list>` são `SELECT *` "açúcar sintático" e podem ser expressos alternativamente utilizando declarações simples SELECT como mostrado abaixo.  
  
1. `SELECT * FROM ... AS from_alias ...`  
  
   equivale a:  
  
   `SELECT from_alias FROM ... AS from_alias ...`  
  
2. `SELECT <expr1> AS p1, <expr2> AS p2,..., <exprN> AS pN [other clauses...]`  
  
   equivale a:  
  
   `SELECT VALUE { p1: <expr1>, p2: <expr2>, ..., pN: <exprN> }[other clauses...]`  
  
## <a name="examples"></a>Exemplos

O exemplo de consulta SELECT seguinte regressa `address` de `Families` `id` cujas `AndersenFamily` correspondências:

```sql
    SELECT f.address
    FROM Families f
    WHERE f.id = "AndersenFamily"
```

Os resultados são:

```json
    [{
      "address": {
        "state": "WA",
        "county": "King",
        "city": "Seattle"
      }
    }]
```

## <a name="next-steps"></a>Passos seguintes

- [Introdução](sql-query-getting-started.md)
- [Amostras de Azure Cosmos DB .NET](https://github.com/Azure/azure-cosmos-dotnet-v3)
- [Cláusula WHERE](sql-query-where.md)
