---
title: ONDE a cláusula em Azure Cosmos DB
description: Conheça a cláusula SQL WHERE para Azure Cosmos DB
author: timsander1
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 03/06/2020
ms.author: tisande
ms.openlocfilehash: 483a0533eafc81ef8698d260a753062ae074f6d4
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/28/2020
ms.locfileid: "78898788"
---
# <a name="where-clause-in-azure-cosmos-db"></a>ONDE a cláusula em Azure Cosmos DB

A cláusula opcional`WHERE <filter_condition>`WHERE () especifica condição(s) que os itens JSON de origem devem satisfazer para que a consulta os inclua nos resultados. Um item JSON deve avaliar `true` as condições especificadas a considerar para o resultado. A camada de índice utiliza a cláusula WHERE para determinar o menor subconjunto de itens de origem que podem fazer parte do resultado.
  
## <a name="syntax"></a>Sintaxe
  
```sql  
WHERE <filter_condition>  
<filter_condition> ::= <scalar_expression>  
  
```  
  
## <a name="arguments"></a>Argumentos

- `<filter_condition>`  
  
   Especifica a condição a cumprir para que os documentos sejam devolvidos.  
  
- `<scalar_expression>`  
  
   Expressão que representa o valor a calcular. Consulte [expressões scalar](sql-query-scalar-expressions.md) para mais detalhes.  
  
## <a name="remarks"></a>Observações
  
  Para que o documento seja devolvido, uma expressão especificada como condição do filtro deve avaliar com a verdade. Apenas o `true` valor booleano satisfará a condição, qualquer outro valor: indefinido, nulo, falso, número, matriz ou objeto não satisfará a condição.

  Se incluir a sua `WHERE` chave de partição na cláusula como parte de um filtro de igualdade, a sua consulta filtrará automaticamente apenas para as divisórias relevantes.

## <a name="examples"></a>Exemplos

A seguinte consulta solicita itens que `id` contenham um `AndersenFamily`imóvel cujo valor é . Exclui qualquer item que não `id` tenha uma propriedade ou `AndersenFamily`cujo valor não corresponda.

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

### <a name="scalar-expressions-in-the-where-clause"></a>Expressões escalar na cláusula WHERE

O exemplo anterior mostrou uma simples consulta de igualdade. A API SQL também suporta [várias expressões escalar.](sql-query-scalar-expressions.md) Os mais utilizados são expressões binárias e não-secundárias. Referências de propriedade do objeto JSON fonte também são expressões válidas.

Pode utilizar os seguintes operadores binários suportados:  

|**Tipo de operadores**  | **Valores** |
|---------|---------|
|Aritmética | +,-,*,/,% |
|Bitwise    | \|, &,  <<, >>, >>>  (turno direito de preenchimento zero) |
|Lógico    | E, OU, NÃO      |
|Comparação | =, &lt;!=, &gt; &lt;=, &gt;=, <> |
|String     |  \|\|(concatenato) |

As seguintes consultas utilizam operadores binários:

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

Também pode utilizar os operadores não-,-, ~, e NÃO em consultas, como mostram os seguintes exemplos:

```sql
    SELECT *
    FROM Families.children[0] c
    WHERE NOT(c.grade = 5)  -- matching grades == 1

    SELECT *
    FROM Families.children[0] c
    WHERE (-c.grade = -5)  -- matching grades == 5
```

Também pode usar referências de propriedade em consultas. Por exemplo, `SELECT * FROM Families f WHERE f.isRegistered` devolve o item JSON contendo o imóvel `isRegistered` com valor igual a `true`. Qualquer outro valor, `false` `null`como, `<number>` `<string>`, `<object>` `Undefined`, `<array>`, ou , exclui o item do resultado.

## <a name="next-steps"></a>Passos seguintes

- [Introdução](sql-query-getting-started.md)
- [Na palavra-chave](sql-query-keywords.md#in)
- [Cláusula FROM](sql-query-from.md)