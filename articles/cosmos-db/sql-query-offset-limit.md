---
title: Cláusula OFFSET LIMIT em Azure Cosmos DB
description: Saiba como usar a cláusula OFFSET LIMIT para saltar e tomar alguns valores ao consultar em Azure Cosmos DB
author: timsander1
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 07/29/2020
ms.author: tisande
ms.openlocfilehash: 9342c0c10ed4f7287716d21823fddfe992e0568f
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/09/2020
ms.locfileid: "87432722"
---
# <a name="offset-limit-clause-in-azure-cosmos-db"></a>Cláusula OFFSET LIMIT em Azure Cosmos DB

A cláusula OFFSET LIMIT é uma cláusula opcional a ignorar e, em seguida, tirar alguns valores da consulta. A contagem OFFSET e a contagem DE LIMITE são exigidas na cláusula OFFSET LIMIT.

Quando o OFFSET LIMIT é utilizado em conjunto com uma cláusula ORDER BY, o conjunto de resultados é produzido fazendo saltar e assumir os valores ordenados. Se não for utilizada nenhuma cláusula ORDER BY, resultará numa ordem determinística de valores.

## <a name="syntax"></a>Sintaxe
  
```sql  
OFFSET <offset_amount> LIMIT <limit_amount>
```  
  
## <a name="arguments"></a>Argumentos

- `<offset_amount>`

   Especifica o número inteiro de itens que os resultados da consulta devem saltar.

- `<limit_amount>`
  
   Especifica o número inteiro de itens que os resultados da consulta devem incluir

## <a name="remarks"></a>Observações
  
  Tanto a `OFFSET` contagem como a contagem são `LIMIT` necessárias na `OFFSET LIMIT` cláusula. Se for utilizada uma `ORDER BY` cláusula opcional, o conjunto de resultados é produzido fazendo o salto sobre os valores ordenados. Caso contrário, a consulta devolverá uma ordem fixa de valores.

  A taxa RU de uma consulta `OFFSET LIMIT` irá aumentar à medida que o número de termos que estão sendo compensados aumenta. Para consultas que tenham [várias páginas de resultados,](sql-query-pagination.md)recomendamos normalmente a utilização de [fichas de continuação.](sql-query-pagination.md#continuation-tokens) As fichas de continuação são um "marcador" para o local onde a consulta pode ser retomada mais tarde. Se `OFFSET LIMIT` utilizar, não existe "marcador". Se quiser devolver a próxima página da consulta, terá de começar do início.
  
  Deve usar `OFFSET LIMIT` para casos em que gostaria de saltar totalmente os itens e poupar recursos do cliente. Por exemplo, deve utilizar `OFFSET LIMIT` se quiser saltar para o resultado da 1000ª consulta e não tiver necessidade de visualizar os resultados 1 a 999. No backend, `OFFSET LIMIT` ainda carrega cada item, incluindo os que são ignorados. A vantagem de desempenho é uma poupança nos recursos dos clientes, evitando o processamento de itens que não são necessários.

## <a name="examples"></a>Exemplos

Por exemplo, aqui está uma consulta que salta o primeiro valor e devolve o segundo valor (por ordem do nome da cidade residente):

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

Aqui está uma consulta que salta o primeiro valor e devolve o segundo valor (sem encomendar):

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

## <a name="next-steps"></a>Passos seguintes

- [Introdução](sql-query-getting-started.md)
- [Cláusula SELECT](sql-query-select.md)
- [ORDEM POR CLÁUSULA](sql-query-order-by.md)
