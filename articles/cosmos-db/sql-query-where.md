---
title: CLÁUSULA ONDE em Azure Cosmos DB
description: Saiba mais sobre a cláusula SQL WHERE para Azure Cosmos DB
author: timsander1
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 03/06/2020
ms.author: tisande
ms.openlocfilehash: ceffb203ccc2cca1ff6e1c53644cde955c2e0acb
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/09/2020
ms.locfileid: "86523507"
---
# <a name="where-clause-in-azure-cosmos-db"></a>CLÁUSULA ONDE em Azure Cosmos DB

A cláusula OPCIONAL WHERE `WHERE <filter_condition>` () especifica a condição(s) que os itens JSON de origem devem satisfazer para que a consulta os inclua em resultados. Um item JSON deve avaliar as condições especificadas `true` a considerar para o resultado. A camada de índice utiliza a cláusula WHERE para determinar o subconjunto mais pequeno de itens de origem que podem fazer parte do resultado.
  
## <a name="syntax"></a>Sintaxe
  
```sql  
WHERE <filter_condition>  
<filter_condition> ::= <scalar_expression>  
  
```  
  
## <a name="arguments"></a>Argumentos

- `<filter_condition>`  
  
   Especifica a condição a cumprir para que os documentos sejam devolvidos.  
  
- `<scalar_expression>`  
  
   Expressão que representa o valor a calcular. Consulte [as expressões scalar](sql-query-scalar-expressions.md) para mais detalhes.  
  
## <a name="remarks"></a>Observações
  
  Para que o documento seja devolvido, uma expressão especificada como condição de filtro deve avaliar a verdade. Apenas o valor `true` booleano irá satisfazer a condição, qualquer outro valor: indefinido, nulo, falso, número, matriz ou objeto não satisfaz a condição.

  Se incluir a sua chave de partição na `WHERE` cláusula como parte de um filtro de igualdade, a sua consulta filtrará automaticamente apenas as divisórias relevantes.

## <a name="examples"></a>Exemplos

A consulta seguinte solicita itens que contenham um `id` imóvel cujo valor seja `AndersenFamily` . Exclui qualquer item que não tenha uma `id` propriedade ou cujo valor não corresponda. `AndersenFamily`

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

O exemplo anterior mostrou uma simples consulta sobre a igualdade. A API SQL também suporta várias [expressões escalares.](sql-query-scalar-expressions.md) As expressões mais usadas são binárias e não-árias. As referências de propriedade do objeto JSON de origem também são expressões válidas.

Pode utilizar os seguintes operadores binários suportados:  

|**Tipo de operadores**  | **Valores** |
|---------|---------|
|Aritmético | +,-,*,/,% |
|Bit a bit    | \|, &, ^, <<, >> >>>  (turno certo de preenchimento zero) |
|Lógico    | E, OU, NÃO      |
|Comparação | =, !=, &lt; &gt; , &lt; =, &gt; =, <> |
|Cadeia     |  \|\| (concatenato) |

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

Também pode utilizar os operadores não ,-, ~, e NÃO em consultas, como mostram os seguintes exemplos:

```sql
    SELECT *
    FROM Families.children[0] c
    WHERE NOT(c.grade = 5)  -- matching grades == 1

    SELECT *
    FROM Families.children[0] c
    WHERE (-c.grade = -5)  -- matching grades == 5
```

Você também pode usar referências de propriedade em consultas. Por exemplo, `SELECT * FROM Families f WHERE f.isRegistered` devolve o item JSON que contém a propriedade `isRegistered` com valor igual a `true` . Qualquer outro valor, como, por exemplo, , `false` , ou , exclui o item do `null` `Undefined` `<number>` `<string>` `<object>` `<array>` resultado. Além disso, pode utilizar a `IS_DEFINED` função de verificação de tipo para consultar com base na presença ou ausência de uma determinada propriedade JSON. Por exemplo, `SELECT * FROM Families f WHERE NOT IS_DEFINED(f.isRegistered)` devolve qualquer item JSON que não tenha um valor para `isRegistered` .

## <a name="next-steps"></a>Passos seguintes

- [Introdução](sql-query-getting-started.md)
- [IN palavra-chave](sql-query-keywords.md#in)
- [A partir da cláusula](sql-query-from.md)
