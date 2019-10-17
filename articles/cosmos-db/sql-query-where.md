---
title: Cláusula WHERE em Azure Cosmos DB
description: Saiba mais sobre a cláusula WHERE do SQL para Azure Cosmos DB
author: markjbrown
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 06/10/2019
ms.author: mjbrown
ms.openlocfilehash: cd5643d8be06afcd43c5bfe38d6f5e9caa6f906e
ms.sourcegitcommit: 1d0b37e2e32aad35cc012ba36200389e65b75c21
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/15/2019
ms.locfileid: "72326643"
---
# <a name="where-clause-in-azure-cosmos-db"></a>Cláusula WHERE em Azure Cosmos DB

A cláusula WHERE opcional (`WHERE <filter_condition>`) especifica a (s) condição (ões) que os itens JSON de origem devem satisfazer para que a consulta as inclua nos resultados. Um item JSON deve avaliar as condições especificadas para `true` a serem consideradas para o resultado. A camada de índice usa a cláusula WHERE para determinar o menor subconjunto de itens de origem que podem fazer parte do resultado.
  
## <a name="syntax"></a>Sintaxe
  
```sql  
WHERE <filter_condition>  
<filter_condition> ::= <scalar_expression>  
  
```  
  
## <a name="arguments"></a>Argumentos

- `<filter_condition>`  
  
   Especifica a condição a ser atendida para os documentos a serem retornados.  
  
- `<scalar_expression>`  
  
   Expressão que representa o valor a ser computado. Consulte [expressões escalares](sql-query-scalar-expressions.md) para obter detalhes.  
  

## <a name="remarks"></a>Observações
  
  Para que o documento seja retornado, uma expressão especificada como condição de filtro deve ser avaliada como true. Somente o valor booliano true atenderá à condição, qualquer outro valor: indefinido, nulo, falso, número, matriz ou objeto não atenderá à condição. 

## <a name="examples"></a>Exemplos

A consulta a seguir solicita itens que contêm uma propriedade `id` cujo valor é `AndersenFamily`. Ele exclui qualquer item que não tenha uma propriedade `id` ou cujo valor não corresponda `AndersenFamily`.

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

### <a name="scalar-expressions-in-the-where-clause"></a>Expressões escalares na cláusula WHERE

O exemplo anterior mostrou uma consulta de igualdade simples. A API do SQL também dá suporte a várias [expressões escalares](sql-query-scalar-expressions.md). As usadas com mais frequência são expressões binárias e unários. Referências de Propriedade do objeto JSON de origem também são expressões válidas.

Você pode usar os seguintes operadores binários com suporte:  

|**Tipo de operador**  | **Valores** |
|---------|---------|
|Operações | +,-,*,/,% |
|Nível    | \|, &, ^, < <, > >, > > > (deslocamento à direita de preenchimento zero) |
|Lógico    | E, OU, NÃO      |
|Comparação | =,! =, &lt;, &gt;, &lt; =, &gt; =, < > |
|String     |  \| @ no__t-1 (concatenar) |

As consultas a seguir usam operadores binários:

```sql
    SELECT *
    FROM Families.children[0] c
    WHERE c.grade % 2 = 1     -- matching grades == 5, 1

    SELECT *
    FROM Families.children[0] c
    WHERE c.grade ^ 4 = 1    -- matching grades == 5

    SELECT *
    FROM Families.children[0] c
    WHERE c.grade >= 5    -- matching grades == 5
```

Você também pode usar os operadores unários +,-, ~, e não em consultas, conforme mostrado nos exemplos a seguir:

```sql
    SELECT *
    FROM Families.children[0] c
    WHERE NOT(c.grade = 5)  -- matching grades == 1

    SELECT *
    FROM Families.children[0] c
    WHERE (-c.grade = -5)  -- matching grades == 5
```

Você também pode usar referências de propriedade em consultas. Por exemplo, `SELECT * FROM Families f WHERE f.isRegistered` retorna o item JSON que contém a propriedade `isRegistered` com valor igual a `true`. Qualquer outro valor, como `false`, `null`, `Undefined`, `<number>`, `<string>`, `<object>` ou `<array>`, exclui o item do resultado. 

## <a name="next-steps"></a>Passos seguintes

- [Introdução](sql-query-getting-started.md)
- [Exemplos do .NET Azure Cosmos DB](https://github.com/Azure/azure-cosmos-dotnet-v3)
- [Cláusula FROM](sql-query-from.md)