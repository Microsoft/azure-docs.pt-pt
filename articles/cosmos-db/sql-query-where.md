---
title: ONDE a cláusula em Azure Cosmos DB
description: Conheça a cláusula SQL WHERE para Azure Cosmos DB
author: timsander1
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 02/03/2020
ms.author: tisande
ms.openlocfilehash: 31653b598f0f3a79bf7f9c09231b1d111f167a16
ms.sourcegitcommit: 4f6a7a2572723b0405a21fea0894d34f9d5b8e12
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 02/04/2020
ms.locfileid: "76982234"
---
# <a name="where-clause-in-azure-cosmos-db"></a>ONDE a cláusula em Azure Cosmos DB

A cláusula opcional WHERE (`WHERE <filter_condition>`) especifica condição(s) que os itens JSON de origem devem satisfazer para que a consulta os inclua nos resultados. Um item JSON deve avaliar as condições especificadas para `true` a considerar para o resultado. A camada de índice utiliza a cláusula WHERE para determinar o menor subconjunto de itens de origem que podem fazer parte do resultado.
  
## <a name="syntax"></a>Sintaxe
  
```sql  
WHERE <filter_condition>  
<filter_condition> ::= <scalar_expression>  
  
```  
  
## <a name="arguments"></a>Argumentos

- `<filter_condition>`  
  
   Especifica as condições para ser atendidas para que os documentos a serem retornados.  
  
- `<scalar_expression>`  
  
   Expressão que representa o valor a ser calculada. Consulte [expressões scalar](sql-query-scalar-expressions.md) para mais detalhes.  
  

## <a name="remarks"></a>Observações
  
  Para que o documento a ser devolvida uma expressão especificada como filtro de condição deve ser avaliado como true. Apenas o valor booleano true satisfaça a condição, qualquer outro valor: indefinido, nulo, FALSO, número, matriz ou objeto não satisfaçam a condição. 

## <a name="examples"></a>Exemplos

A consulta seguinte solicita itens que contenham uma propriedade `id` cujo valor é `AndersenFamily`. Exclui qualquer item que não tenha uma propriedade `id` ou cujo valor não corresponda `AndersenFamily`.

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

O exemplo anterior mostrou uma consulta simples de igualdade. A API SQL também suporta [várias expressões escalar.](sql-query-scalar-expressions.md) O uso mais comum é expressões binário e unário. Referências de propriedade do objeto JSON de origem também são as expressões válidas.

Pode utilizar os seguintes operadores binários suportados:  

|**Tipo de operador**  | **Valores** |
|---------|---------|
|Operações aritméticas | +,-,*,/,% |
|bit a bit    | \|, &, ^, <<>>,, >>> (shift direita do preenchimento de zero) |
|Lógica    | E, EM ALTERNATIVA, NÃO      |
|Comparação | =, !=, &lt;, &gt;, &lt;=, &gt;=, <> |
|Cadeia     |  \|\| (concatenar) |

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

Também pode usar referências de propriedade em consultas. Por exemplo, `SELECT * FROM Families f WHERE f.isRegistered` devolve o item JSON contendo o `isRegistered` de propriedade com valor igual a `true`. Qualquer outro valor, como `false`, `null`, `Undefined`, `<number>`, `<string>`, `<object>`ou `<array>`, exclui o item do resultado.

## <a name="next-steps"></a>Passos seguintes

- [Introdução](sql-query-getting-started.md)
- [Na palavra-chave](sql-query-keywords.md#in)
- [Cláusula FROM](sql-query-from.md)