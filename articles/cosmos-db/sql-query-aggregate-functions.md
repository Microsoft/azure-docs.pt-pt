---
title: Funções agregadas em Azure Cosmos DB
description: Saiba mais sobre a sintaxe de função agregada SQL, tipos de funções agregadas suportadas pela Azure Cosmos DB.
author: timsander1
ms.service: cosmos-db
ms.subservice: cosmosdb-sql
ms.topic: conceptual
ms.date: 12/02/2020
ms.author: tisande
ms.openlocfilehash: c0d953c8d99582f63744d51b505852b5c44bc409
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/29/2021
ms.locfileid: "96555415"
---
# <a name="aggregate-functions-in-azure-cosmos-db"></a>Funções agregadas em Azure Cosmos DB
[!INCLUDE[appliesto-sql-api](includes/appliesto-sql-api.md)]

As funções agregadas realizam um cálculo sobre um conjunto de valores na `SELECT` cláusula e devolvem um único valor. Por exemplo, a seguinte consulta devolve a contagem de itens dentro de um contentor:

```sql
    SELECT COUNT(1)
    FROM c
```

## <a name="types-of-aggregate-functions"></a>Tipos de funções agregadas

A API SQL suporta as seguintes funções agregadas. `SUM` e `AVG` operar com valores numéricos, e `COUNT` trabalhar em `MIN` `MAX` números, cordas, booleanos e nulos.

| Função | Descrição |
|-------|-------------|
| [AVG](sql-query-aggregate-avg.md) | Devolve a média dos valores na expressão. |
| [COUNT](sql-query-aggregate-count.md) | Devolve o número de itens na expressão. |
| [MAX](sql-query-aggregate-max.md) | Devolve o valor máximo da expressão. |
| [MIN](sql-query-aggregate-min.md) | Devolve o valor mínimo na expressão. |
| [SUM](sql-query-aggregate-sum.md) | Devolve a soma de todos os valores da expressão. |


Também pode devolver apenas o valor escalar do agregado utilizando a palavra-chave VALUE. Por exemplo, a seguinte consulta devolve a contagem de valores como um único número:

```sql
    SELECT VALUE COUNT(1)
    FROM Families f
```

Os resultados são:

```json
    [ 2 ]
```

Também pode combinar agregações com filtros. Por exemplo, a seguinte consulta devolve a contagem de itens com o estado de endereço de `WA` .

```sql
    SELECT VALUE COUNT(1)
    FROM Families f
    WHERE f.address.state = "WA"
```

Os resultados são:

```json
    [ 1 ]
```

## <a name="remarks"></a>Observações

Estas funções agregadas do sistema beneficiarão de um [índice de intervalo.](index-policy.md#includeexclude-strategy) Se espera fazer um `AVG` , , ou em um `COUNT` `MAX` `MIN` `SUM` imóvel, deve [incluir o caminho relevante na política de indexação.](index-policy.md#includeexclude-strategy)

## <a name="next-steps"></a>Passos seguintes

- [Introdução ao Azure Cosmos DB](introduction.md)
- [Funções do sistema](sql-query-system-functions.md)
- [Funções definidas pelo utilizador](sql-query-udfs.md)