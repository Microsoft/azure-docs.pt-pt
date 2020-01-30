---
title: Cláusula de limite de deslocamento no Azure Cosmos DB
description: Saiba como usar a cláusula de limite de deslocamento para ignorar e pegar alguns valores ao consultar em Azure Cosmos DB
author: timsander1
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 06/10/2019
ms.author: mjbrown
ms.openlocfilehash: 3d23676885323e370cee1e9cc9e98c7128faf2e0
ms.sourcegitcommit: 984c5b53851be35c7c3148dcd4dfd2a93cebe49f
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 01/28/2020
ms.locfileid: "76771577"
---
# <a name="offset-limit-clause-in-azure-cosmos-db"></a>Cláusula de limite de deslocamento no Azure Cosmos DB

A cláusula de limite de deslocamento é uma cláusula opcional para ignorar e, em seguida, pegar um número de valores da consulta. A contagem de deslocamento e a contagem de limites são necessárias na cláusula de limite de deslocamento.

Quando o limite de deslocamento é usado em conjunto com uma cláusula ORDER BY, o conjunto de resultados é produzido ao fazer a ação de ignorar e assumir os valores ordenados. Se nenhuma cláusula ORDER BY for usada, ela resultará em uma ordem determinística de valores.

## <a name="syntax"></a>Sintaxe
  
```sql  
OFFSET <offset_amount> LIMIT <limit_amount>
```  
  
## <a name="arguments"></a>Argumentos

- `<offset_amount>`

   Especifica o número inteiro de itens que os resultados da consulta devem ignorar.

- `<limit_amount>`
  
   Especifica o número inteiro de itens que os resultados da consulta devem incluir

## <a name="remarks"></a>Observações
  
  Tanto a contagem de `OFFSET` como a contagem `LIMIT` são necessárias na cláusula `OFFSET LIMIT`. Se uma cláusula opcional `ORDER BY` for usada, o conjunto de resultados será produzido fazendo o ignorar os valores ordenados. Caso contrário, a consulta retornará uma ordem fixa de valores.

  A acusação de RU de uma consulta com `OFFSET LIMIT` aumentará à medida que o número de termos a ser compensados aumenta. Para consultas que tenham várias páginas de resultados, recomendamos normalmente o uso de tokens de continuação. Os tokens de continuação são um "marcador" para o local onde a consulta pode ser retomada mais tarde. Se usar `OFFSET LIMIT`, não há "marcador". Se quisesse devolver a próxima página da consulta, teria de começar do início.
  
  Deve usar `OFFSET LIMIT` para casos em que gostaria de ignorar totalmente os documentos e poupar recursos do cliente. Por exemplo, deve usar `OFFSET LIMIT` se quiser saltar para o resultado da 1000ª consulta e não tem necessidade de ver os resultados 1 a 999. Na parte de trás, `OFFSET LIMIT` ainda carrega cada documento, incluindo os que são ignorados. A vantagem de desempenho é uma poupança nos recursos dos clientes evitando o processamento de documentos que não são necessários.

## <a name="examples"></a>Exemplos

Por exemplo, aqui está uma consulta que ignora o primeiro valor e retorna o segundo valor (na ordem do nome da cidade residente):

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

Aqui está uma consulta que ignora o primeiro valor e retorna o segundo valor (sem ordenação):

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
