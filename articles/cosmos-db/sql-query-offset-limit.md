---
title: Cláusula de limite de deslocamento no Azure Cosmos DB
description: Saiba mais sobre a cláusula de limite de deslocamento para o Azure Cosmos DB.
author: timsander1
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 06/10/2019
ms.author: mjbrown
ms.openlocfilehash: 60ac28c80e9f7cc72f4d6005c12cb5f68671341e
ms.sourcegitcommit: a12b2c2599134e32a910921861d4805e21320159
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 06/24/2019
ms.locfileid: "67342793"
---
# <a name="offset-limit-clause"></a>Cláusula de limite de deslocamento

A cláusula de deslocamento limite é uma cláusula opcional para ignorar, dê algum número de valores da consulta. A contagem de deslocamento e a contagem de limite são necessários na cláusula de limite de deslocamento.

Quando o limite de deslocamento é utilizado em conjunto com uma cláusula ORDER BY, o conjunto de resultados é produzido por ignorar a fazer e assumir os valores ordenados. Não se for utilizada nenhuma cláusula ORDER BY, irá resultar numa ordem determinista de valores.

## <a name="syntax"></a>Sintaxe
  
```sql  
OFFSET <offset_amount> LIMIT <limit_amount>
```  
  
## <a name="arguments"></a>Argumentos

- `<offset_amount>`

   Especifica o número de número inteiro de itens que devem ignorar os resultados da consulta.

- `<limit_amount>`
  
   Especifica o número de número inteiro de itens que devem incluir os resultados da consulta

## <a name="remarks"></a>Observações
  
  A contagem de deslocamento e a contagem de limite são necessários na cláusula de limite de deslocamento. Se um opcional `ORDER BY` cláusula é utilizada, o conjunto de resultados é produzido, fazendo a ignorar sobre os valores ordenados. Caso contrário, a consulta retornará uma ordem fixa de valores. Atualmente, esta cláusula é suportada para as consultas existentes apenas uma única partição, ele ainda não suportam consultas entre partições.

## <a name="examples"></a>Exemplos

Por exemplo, eis uma consulta que ignora o primeiro valor e retorna o segundo valor (por ordem do nome da cidade residente):

```sql
    SELECT f.id, f.address.city
    FROM Families f
    ORDER BY f.address.city
    OFFSET 1 LIMIT 1
```

Os resultados são:

```json
    [
      {
        "id": "AndersenFamily",
        "city": "Seattle"
      }
    ]
```

Eis uma consulta que ignora o primeiro valor e retorna o segundo valor (sem ordem):

```sql
   SELECT f.id, f.address.city
    FROM Families f
    OFFSET 1 LIMIT 1
```

Os resultados são:

```json
    [
      {
        "id": "WakefieldFamily",
        "city": "Seattle"
      }
    ]
```

## <a name="next-steps"></a>Passos Seguintes

- [Introdução](sql-query-getting-started.md)
- [Cláusula SELECT](sql-query-select.md)
- [Cláusula ORDER BY](sql-query-order-by.md)
