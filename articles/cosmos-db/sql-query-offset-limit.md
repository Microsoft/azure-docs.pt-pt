---
title: Cláusula LIMITE OFFSET em Azure Cosmos DB
description: Aprenda a usar a cláusula OFFSET LIMIT para saltar e tomar alguns certos valores ao consultar em Azure Cosmos DB
author: timsander1
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 06/10/2019
ms.author: mjbrown
ms.openlocfilehash: 3d23676885323e370cee1e9cc9e98c7128faf2e0
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/28/2020
ms.locfileid: "76771577"
---
# <a name="offset-limit-clause-in-azure-cosmos-db"></a>Cláusula LIMITE OFFSET em Azure Cosmos DB

A cláusula OFFSET LIMIT é uma cláusula opcional para saltar e depois retirar alguns valores da consulta. A contagem OFFSET e a contagem LIMIT são exigidas na cláusula OFFSET LIMIT.

Quando o OFFSET LIMIT é utilizado em conjunto com uma cláusula ORDER BY, o conjunto de resultados é produzido fazendo skip e assume os valores encomendados. Se não for utilizada a cláusula ORDER BY, resultará numa ordem determinística de valores.

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
  
  Tanto `OFFSET` a contagem `LIMIT` como a contagem `OFFSET LIMIT` são exigidas na cláusula. Se for `ORDER BY` utilizada uma cláusula opcional, o conjunto de resultados é produzido fazendo o salto sobre os valores ordenados. Caso contrário, a consulta devolverá uma ordem fixa de valores.

  A carga ru de `OFFSET LIMIT` uma consulta com vai aumentar à medida que o número de termos sendo compensados aumenta. Para consultas que tenham várias páginas de resultados, recomendamos normalmente o uso de tokens de continuação. Os tokens de continuação são um "marcador" para o local onde a consulta pode ser retomada mais tarde. Se usar, `OFFSET LIMIT`não há "marcador". Se quisesse devolver a próxima página da consulta, teria de começar do início.
  
  Deve usar `OFFSET LIMIT` para casos em que gostaria de ignorar totalmente os documentos e poupar recursos do cliente. Por exemplo, deve `OFFSET LIMIT` utilizar se quiser saltar para o resultado da 1000ª consulta e não tem necessidade de ver os resultados 1 a 999. Na parte de `OFFSET LIMIT` trás, ainda carrega cada documento, incluindo aqueles que são ignorados. A vantagem de desempenho é uma poupança nos recursos dos clientes evitando o processamento de documentos que não são necessários.

## <a name="examples"></a>Exemplos

Por exemplo, aqui está uma consulta que ignora o primeiro valor e devolve o segundo valor (por ordem do nome da cidade residente):

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

Aqui está uma consulta que ignora o primeiro valor e devolve o segundo valor (sem encomenda):

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
- [Cláusula ORDER BY](sql-query-order-by.md)
