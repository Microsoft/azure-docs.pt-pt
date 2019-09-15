---
title: Cláusula WHERE em Azure Cosmos DB
description: Saiba mais sobre a cláusula WHERE do SQL para Azure Cosmos DB
author: markjbrown
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 06/10/2019
ms.author: mjbrown
ms.openlocfilehash: 362024868de269ed64a440a25e8c19c5b68bef80
ms.sourcegitcommit: e97a0b4ffcb529691942fc75e7de919bc02b06ff
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 09/15/2019
ms.locfileid: "71003466"
---
# <a name="where-clause"></a>Cláusula WHERE

A cláusula WHERE opcional (`WHERE <filter_condition>`) especifica a (s) condição (ões) que os itens JSON de origem devem satisfazer para que a consulta as inclua nos resultados. Um item JSON deve avaliar as condições `true` especificadas a serem consideradas para o resultado. A camada de índice usa a cláusula WHERE para determinar o menor subconjunto de itens de origem que podem fazer parte do resultado.
  
## <a name="syntax"></a>Sintaxe
  
```sql  
WHERE <filter_condition>  
<filter_condition> ::= <scalar_expression>  
  
```  
  
## <a name="arguments"></a>Argumentos

- `<filter_condition>`  
  
   Especifica as condições para ser atendidas para que os documentos a serem retornados.  
  
- `<scalar_expression>`  
  
   Expressão que representa o valor a ser calculada. Consulte [expressões escalares](sql-query-scalar-expressions.md) para obter detalhes.  
  

## <a name="remarks"></a>Observações
  
  Para que o documento a ser devolvida uma expressão especificada como filtro de condição deve ser avaliado como true. Apenas o valor booleano true satisfaça a condição, qualquer outro valor: indefinido, nulo, FALSO, número, matriz ou objeto não satisfaçam a condição. 

## <a name="examples"></a>Exemplos

A consulta a seguir solicita itens que contêm `id` uma propriedade cujo valor `AndersenFamily`é. Ele exclui qualquer item que não tenha uma `id` propriedade ou cujo valor não corresponda. `AndersenFamily`

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

O exemplo anterior mostrou uma consulta simples de igualdade. A API do SQL também dá suporte a várias [expressões escalares](sql-query-scalar-expressions.md). O uso mais comum é expressões binário e unário. Referências de propriedade do objeto JSON de origem também são as expressões válidas.

Você pode usar os seguintes operadores binários com suporte:  

|**Tipo de operador**  | **Valores** |
|---------|---------|
|Operações aritméticas | +,-,*,/,% |
|bit a bit    | \|, &, ^, <<>>,, >>> (shift direita do preenchimento de zero) |
|Lógica    | E, EM ALTERNATIVA, NÃO      |
|Comparação | =, !=, &lt;, &gt;, &lt;=, &gt;=, <> |
|Cadeia     |  \|\| (concatenar) |

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

Você também pode usar referências de propriedade em consultas. Por exemplo, `SELECT * FROM Families f WHERE f.isRegistered` retorna o item JSON que contém a `isRegistered` Propriedade com valor igual `true`a. Qualquer `false`outro valor, como `Undefined`, `null` `<number>` ,,`<object>`, ,ou`<array>`, exclui o item do resultado. `<string>` 

## <a name="next-steps"></a>Passos seguintes

- [Introdução](sql-query-getting-started.md)
- [Exemplos do Azure Cosmos DB .NET](https://github.com/Azure/azure-cosmos-dotnet-v3)
- [Cláusula FROM](sql-query-from.md)