---
title: Cláusula SELECT no Azure Cosmos DB
description: Saiba mais sobre a cláusula SQL SELECT para Azure Cosmos DB. Use SQL como uma linguagem de consulta JSON Azure Cosmos DB.
author: ginarobinson
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 06/10/2019
ms.author: girobins
ms.openlocfilehash: b90fc6f1f50ec2ea75619188cca36f78061f28df
ms.sourcegitcommit: 1d0b37e2e32aad35cc012ba36200389e65b75c21
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/15/2019
ms.locfileid: "72326794"
---
# <a name="select-clause-in-azure-cosmos-db"></a>Cláusula SELECT no Azure Cosmos DB

Cada consulta consiste em uma cláusula SELECT e em cláusulas opcional from e [Where](sql-query-where.md) , por padrões [de](sql-query-from.md) SQL ANSI. Normalmente, a origem na cláusula FROM é enumerada e a cláusula WHERE aplica um filtro na origem para recuperar um subconjunto de itens JSON. Em seguida, a cláusula SELECT projeta os valores JSON solicitados na lista de seleção.

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

  Propriedades ou valor a ser selecionado para o conjunto de resultados.  
  
- `'*'`  

  Especifica que o valor deve ser recuperado sem fazer nenhuma alteração. Especificamente, se o valor processado for um objeto, todas as propriedades serão recuperadas.  
  
- `<object_property_list>`  
  
  Especifica a lista de propriedades a serem recuperadas. Cada valor retornado será um objeto com as propriedades especificadas.  
  
- `VALUE`  

  Especifica que o valor JSON deve ser recuperado em vez do objeto JSON completo. Isso, ao contrário de `<property_list>`, não encapsula o valor projetado em um objeto.  
 
- `DISTINCT`
  
  Especifica que duplicatas de propriedades projetadas devem ser removidas.  

- `<scalar_expression>`  

  Expressão que representa o valor a ser computado. Consulte a seção [expressões escalares](sql-query-scalar-expressions.md) para obter detalhes.  

## <a name="remarks"></a>Observações

A sintaxe `SELECT *` só será válida se a cláusula FROM tiver declarado exatamente um alias. `SELECT *` fornece uma projeção de identidade, que pode ser útil se nenhuma projeção for necessária. SELECT * só será válida se a cláusula FROM for especificada e introduzida apenas uma única fonte de entrada.  
  
Tanto `SELECT <select_list>` quanto `SELECT *` são "baixas sintáticas" e podem ser expressas como alternativa usando instruções SELECT simples, conforme mostrado abaixo.  
  
1. `SELECT * FROM ... AS from_alias ...`  
  
   é equivalente a:  
  
   `SELECT from_alias FROM ... AS from_alias ...`  
  
2. `SELECT <expr1> AS p1, <expr2> AS p2,..., <exprN> AS pN [other clauses...]`  
  
   é equivalente a:  
  
   `SELECT VALUE { p1: <expr1>, p2: <expr2>, ..., pN: <exprN> }[other clauses...]`  
  
## <a name="examples"></a>Exemplos

O exemplo de consulta SELECT a seguir retorna `address` de `Families` cujos `id` corresponde a `AndersenFamily`:

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

### <a name="quoted-property-accessor"></a>Acessador de propriedade entre aspas
Você pode acessar as propriedades usando o operador de propriedade entre aspas []. Por exemplo, `SELECT c.grade` e `SELECT c["grade"]` são equivalentes. Essa sintaxe é útil para escapar de uma propriedade que contém espaços, caracteres especiais ou tem o mesmo nome que uma palavra-chave SQL ou reservada.

```sql
    SELECT f["lastName"]
    FROM Families f
    WHERE f["id"] = "AndersenFamily"
```

### <a name="nested-properties"></a>Propriedades aninhadas

O exemplo a seguir projeta duas propriedades aninhadas, `f.address.state` e `f.address.city`.

```sql
    SELECT f.address.state, f.address.city
    FROM Families f
    WHERE f.id = "AndersenFamily"
```

Os resultados são:

```json
    [{
      "state": "WA",
      "city": "Seattle"
    }]
```
### <a name="json-expressions"></a>Expressões JSON

A projeção também oferece suporte a expressões JSON, conforme mostrado no exemplo a seguir:

```sql
    SELECT { "state": f.address.state, "city": f.address.city, "name": f.id }
    FROM Families f
    WHERE f.id = "AndersenFamily"
```

Os resultados são:

```json
    [{
      "$1": {
        "state": "WA",
        "city": "Seattle",
        "name": "AndersenFamily"
      }
    }]
```

No exemplo anterior, a cláusula SELECT precisa criar um objeto JSON e, como o exemplo não fornece nenhuma chave, a cláusula usa o nome da variável de argumento implícito `$1`. A consulta a seguir retorna duas variáveis de argumento implícitas: `$1` e `$2`.

```sql
    SELECT { "state": f.address.state, "city": f.address.city },
           { "name": f.id }
    FROM Families f
    WHERE f.id = "AndersenFamily"
```

Os resultados são:

```json
    [{
      "$1": {
        "state": "WA",
        "city": "Seattle"
      }, 
      "$2": {
        "name": "AndersenFamily"
      }
    }]
```

## <a name="next-steps"></a>Passos seguintes

- [Introdução](sql-query-getting-started.md)
- [Exemplos do .NET Azure Cosmos DB](https://github.com/Azure/azure-cosmos-dotnet-v3)
- [Cláusula WHERE](sql-query-where.md)