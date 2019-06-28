---
title: Cláusula WHERE no Azure Cosmos DB
description: Saiba mais sobre a cláusula WHERE de SQL do Azure Cosmos DB
author: markjbrown
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 06/10/2019
ms.author: mjbrown
ms.openlocfilehash: 6a942e48ffea7785fe971cc2f8fa66e8569ed672
ms.sourcegitcommit: a12b2c2599134e32a910921861d4805e21320159
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 06/24/2019
ms.locfileid: "67342944"
---
# <a name="where-clause"></a>Cláusula WHERE

A cláusula WHERE opcional (`WHERE <filter_condition>`) Especifica as condições que os itens JSON de origem tem de cumprir para a consulta para as incluir nos resultados. Um item JSON tem de avaliar as condições especificadas para `true` para ser considerado para o resultado. A camada de índice utiliza a cláusula WHERE para determinar o subconjunto mais pequeno de itens de origem que podem fazer parte do resultado.
  
## <a name="syntax"></a>Sintaxe
  
```sql  
WHERE <filter_condition>  
<filter_condition> ::= <scalar_expression>  
  
```  
  
## <a name="arguments"></a>Argumentos

- `<filter_condition>`  
  
   Especifica as condições para ser atendidas para que os documentos a serem retornados.  
  
- `<scalar_expression>`  
  
   Expressão que representa o valor a ser calculada. Ver [expressões escalares](sql-query-scalar-expressions.md) para obter detalhes.  
  

## <a name="remarks"></a>Observações
  
  Para que o documento a ser devolvida uma expressão especificada como filtro de condição deve ser avaliado como true. Apenas o valor booleano true satisfaça a condição, qualquer outro valor: indefinido, nulo, FALSO, número, matriz ou objeto não satisfaçam a condição. 

## <a name="examples"></a>Exemplos

A seguinte consulta itens de pedidos que contêm uma `id` cujo valor é de propriedade `AndersenFamily`. Ela exclui qualquer item que não tem um `id` propriedade ou cujo valor não corresponde ao `AndersenFamily`.

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

### <a name="scalar-expressions-in-the-where-clause"></a>Escalares expressões na cláusula WHERE

O exemplo anterior mostrou uma consulta simples de igualdade. A API de SQL também suporta várias [expressões escalares](sql-query-scalar-expressions.md). O uso mais comum é expressões binário e unário. Referências de propriedade do objeto JSON de origem também são as expressões válidas.

Pode utilizar os seguintes operadores binários suportados:  

|**Tipo de operador**  | **Valores** |
|---------|---------|
|Operações aritméticas | +,-,*,/,% |
|bit a bit    | \|, &, ^, <<>>,, >>> (shift direita do preenchimento de zero) |
|Lógica    | E, EM ALTERNATIVA, NÃO      |
|Comparação | =, !=, &lt;, &gt;, &lt;=, &gt;=, <> |
|Cadeia     |  \|\| (concatenar) |

As seguintes consultas de usam operadores binários:

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

Também pode utilizar os operadores unários +,-, ~ e não em consultas, conforme mostrado nos exemplos a seguir:

```sql
    SELECT *
    FROM Families.children[0] c
    WHERE NOT(c.grade = 5)  -- matching grades == 1

    SELECT *
    FROM Families.children[0] c
    WHERE (-c.grade = -5)  -- matching grades == 5
```

Também pode utilizar referências de propriedade em consultas. Por exemplo, `SELECT * FROM Families f WHERE f.isRegistered` devolve o item JSON que contém a propriedade `isRegistered` com valor igual a `true`. Qualquer outro valor, tal como `false`, `null`, `Undefined`, `<number>`, `<string>`, `<object>`, ou `<array>`, exclui o item do resultado. 

## <a name="next-steps"></a>Passos Seguintes

- [Introdução](sql-query-getting-started.md)
- [Exemplos do Azure Cosmos DB .NET](https://github.com/Azure/azure-cosmosdb-dotnet)
- [Cláusula FROM](sql-query-from.md)