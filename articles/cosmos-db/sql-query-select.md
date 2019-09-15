---
title: Cláusula SELECT no Azure Cosmos DB
description: Saiba mais sobre a cláusula SQL SELECT para Azure Cosmos DB. Use SQL como uma linguagem de consulta JSON Azure Cosmos DB.
author: ginarobinson
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 06/10/2019
ms.author: girobins
ms.openlocfilehash: d34b1c39d9789409dc365cd4cf07fdc3d5a780fd
ms.sourcegitcommit: e97a0b4ffcb529691942fc75e7de919bc02b06ff
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 09/15/2019
ms.locfileid: "71003525"
---
# <a name="select-clause"></a>Cláusula SELECT

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

  Especifica que o valor deve ser obtido sem fazer quaisquer alterações. Especificamente, se o valor processado é um objeto, serão possível obter todas as propriedades.  
  
- `<object_property_list>`  
  
  Especifica a lista de propriedades a serem obtidas. Cada valor retornado será um objeto com as propriedades especificadas.  
  
- `VALUE`  

  Especifica que o valor JSON deve ser obtido em vez do objeto JSON completo. Isso, ao contrário `<property_list>` não envolve o valor previsto num objeto.  
 
- `DISTINCT`
  
  Especifica que duplicatas de propriedades projetadas devem ser removidas.  

- `<scalar_expression>`  

  Expressão que representa o valor a ser calculada. Ver [expressões escalares](sql-query-scalar-expressions.md) secção para obter detalhes.  

## <a name="remarks"></a>Observações

O `SELECT *` sintaxe só é válida se a cláusula FROM tem declarado exatamente um alias. `SELECT *` Fornece uma projeção de identidade, o que pode ser útil se não projeção é necessária. SELECIONE * só é válido se for especificado cláusula FROM e introduziu apenas uma única origem de entrada.  
  
Ambos `SELECT <select_list>` e `SELECT *` são "açúcar sintático" e pode ser expresso como alternativa ao utilizar as instruções SELECT simples, conforme mostrado abaixo.  
  
1. `SELECT * FROM ... AS from_alias ...`  
  
   é equivalente a:  
  
   `SELECT from_alias FROM ... AS from_alias ...`  
  
2. `SELECT <expr1> AS p1, <expr2> AS p2,..., <exprN> AS pN [other clauses...]`  
  
   é equivalente a:  
  
   `SELECT VALUE { p1: <expr1>, p2: <expr2>, ..., pN: <exprN> }[other clauses...]`  
  
## <a name="examples"></a>Exemplos

O exemplo de consulta Select a `address` seguir `Families` retorna `id` de `AndersenFamily`cujas correspondências:

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

O exemplo a seguir projeta duas propriedades `f.address.state` aninhadas e. `f.address.city`

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

No exemplo anterior, a cláusula SELECT precisa criar um objeto JSON e, como o exemplo não fornece nenhuma chave, a cláusula usa o nome `$1`da variável de argumento implícito. A consulta a seguir retorna duas variáveis de argumento `$1` implícitas: e `$2`.

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

## <a name="next-steps"></a>Passos Seguintes

- [Introdução](sql-query-getting-started.md)
- [Exemplos do Azure Cosmos DB .NET](https://github.com/Azure/azure-cosmos-dotnet-v3)
- [Cláusula WHERE](sql-query-where.md)