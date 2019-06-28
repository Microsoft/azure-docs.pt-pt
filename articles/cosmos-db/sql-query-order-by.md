---
title: Cláusula ORDER BY na Azure Cosmos DB
description: Saiba mais sobre a cláusula SQL ORDER BY para o Azure Cosmos DB. Utilize o SQL como uma linguagem de consulta JSON do Azure Cosmos DB.
author: markjbrown
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 06/10/2019
ms.author: mjbrown
ms.openlocfilehash: d0a1ed33d5848c3ed8d5f83af8b320d77fe0dc65
ms.sourcegitcommit: a12b2c2599134e32a910921861d4805e21320159
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 06/24/2019
ms.locfileid: "67342701"
---
# <a name="order-by-clause"></a>Cláusula ORDER BY

A cláusula ORDER BY opcional especifica a ordem de classificação para obter os resultados devolvidos pela consulta.

## <a name="syntax"></a>Sintaxe
  
```sql  
ORDER BY <sort_specification>  
<sort_specification> ::= <sort_expression> [, <sort_expression>]  
<sort_expression> ::= {<scalar_expression> [ASC | DESC]} [ ,...n ]  
```  

## <a name="arguments"></a>Argumentos
  
- `<sort_specification>`  
  
   Especifica uma expressão no qual ordenar o conjunto de resultados de consulta ou de propriedade. Uma coluna de ordenação pode ser especificada como um alias de nome ou propriedade.  
  
   É possível especificar várias propriedades. Nomes de propriedade tem de ser exclusivos. A sequência das propriedades do tipo na cláusula ORDER BY define a organização do conjunto de resultados classificados. Ou seja, o conjunto de resultados é ordenado pela primeira propriedade e, em seguida, essa lista ordenada é ordenada pela propriedade segundo e assim por diante.  
  
   Os nomes de propriedade referenciados na cláusula ORDER BY tem de corresponder a qualquer um de uma propriedade na lista de seleção ou para uma propriedade definida na coleção especificada na cláusula FROM sem qualquer ambigüidade.  
  
- `<sort_expression>`  
  
   Especifica um ou mais propriedades ou expressões no qual ordenar o conjunto de resultados de consulta.  
  
- `<scalar_expression>`  
  
   Consulte a [expressões escalares](sql-query-scalar-expressions.md) secção para obter detalhes.  
  
- `ASC | DESC`  
  
   Especifica que os valores na coluna especificada devem ser classificados em ordem ascendente ou descendente. ASC ordena a partir do valor mais baixo valor mais alto. DESC ordena a partir do valor mais alto valor mais baixo. ASC é a ordem de classificação padrão. Valores nulos são tratados como os valores possíveis mais baixos.  
  
## <a name="remarks"></a>Observações  
  
   A cláusula ORDER BY requer que a política de indexação incluem um índice para os campos que está a ser ordenados. O tempo de execução de consulta do Azure Cosmos DB suporta a classificação em relação a um nome de propriedade e não contra as propriedades calculadas. Azure Cosmos DB suporta várias propriedades de ORDER BY. Para executar uma consulta com várias propriedades de ORDER BY, deve definir um [índice composto](index-policy.md#composite-indexes) nos campos de que está a ser ordenados.

## <a name="examples"></a>Exemplos

Por exemplo, eis uma consulta que obtém as famílias em ordem ascendente do nome da cidade residente:

```sql
    SELECT f.id, f.address.city
    FROM Families f
    ORDER BY f.address.city
```

Os resultados são:

```json
    [
      {
        "id": "WakefieldFamily",
        "city": "NY"
      },
      {
        "id": "AndersenFamily",
        "city": "Seattle"
      }
    ]
```

A seguinte consulta obtém família `id`s por ordem de sua data de criação de item. Item `creationDate` é um número que representa o *hora "Epoch"* , ou o tempo decorrido desde 1 de Janeiro de 1970 em segundos.

```sql
    SELECT f.id, f.creationDate
    FROM Families f
    ORDER BY f.creationDate DESC
```

Os resultados são:

```json
    [
      {
        "id": "WakefieldFamily",
        "creationDate": 1431620462
      },
      {
        "id": "AndersenFamily",
        "creationDate": 1431620472
      }
    ]
```

Além disso, pode ordenar por várias propriedades. Necessita de uma consulta que ordena por várias propriedades de um [índice composto](index-policy.md#composite-indexes). Considere a seguinte consulta:

```sql
    SELECT f.id, f.creationDate
    FROM Families f
    ORDER BY f.address.city ASC, f.creationDate DESC
```

Esta consulta obtém a família `id` em ordem ascendente do nome de cidade. Se vários itens têm o mesmo nome de cidade, a consulta será ordenar pelo `creationDate` por ordem descendente.

## <a name="next-steps"></a>Passos Seguintes

- [Introdução](sql-query-getting-started.md)
- [Cláusula SELECT](sql-query-select.md)
- [Cláusula de limite de deslocamento](sql-query-offset-limit.md)
