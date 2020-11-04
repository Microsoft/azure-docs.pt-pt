---
title: Funções agregadas em Azure Cosmos DB
description: Saiba mais sobre a sintaxe de função agregada SQL, tipos de funções agregadas suportadas pela Azure Cosmos DB.
author: timsander1
ms.service: cosmos-db
ms.subservice: cosmosdb-sql
ms.topic: conceptual
ms.date: 03/16/2020
ms.author: tisande
ms.openlocfilehash: 7c988f379e94bf2f69854c90d45af42fe2a7ec4f
ms.sourcegitcommit: fa90cd55e341c8201e3789df4cd8bd6fe7c809a3
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 11/04/2020
ms.locfileid: "93332785"
---
# <a name="aggregate-functions-in-azure-cosmos-db"></a>Funções agregadas em Azure Cosmos DB
[!INCLUDE[appliesto-sql-api](includes/appliesto-sql-api.md)]

As funções agregadas realizam um cálculo sobre um conjunto de valores na `SELECT` cláusula e devolvem um único valor. Por exemplo, a seguinte consulta devolve a contagem de itens dentro do `Families` contentor:

## <a name="examples"></a>Exemplos

```sql
    SELECT COUNT(1)
    FROM Families f
```

Os resultados são:

```json
    [{
        "$1": 2
    }]
```

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

## <a name="types-of-aggregate-functions"></a>Tipos de funções agregadas

A API SQL suporta as seguintes funções agregadas. `SUM` e `AVG` operar com valores numéricos, e `COUNT` trabalhar em `MIN` `MAX` números, cordas, booleanos e nulos.

| Função | Descrição |
|-------|-------------|
| CONTAGEM | Devolve o número de itens na expressão. |
| SUM   | Devolve a soma de todos os valores da expressão. |
| MIN   | Devolve o valor mínimo na expressão. |
| MAX   | Devolve o valor máximo da expressão. |
| AVG   | Devolve a média dos valores na expressão. |

Também pode agregar os resultados de uma iteração de matrizes.

> [!NOTE]
> No Data Explorer do portal Azure, as consultas de agregação podem agregar resultados parciais em apenas uma página de consulta. O SDK produz um único valor cumulativo em todas as páginas. Para efetuar consultas de agregação utilizando código, precisa de .NET SDK 1.12.0, .NET Core SDK 1.1.0 ou Java SDK 1.9.5 ou superior.

## <a name="remarks"></a>Observações

Estas funções agregadas do sistema beneficiarão de um [índice de intervalo.](index-policy.md#includeexclude-strategy) Se espera fazer um `COUNT` , , , , ou em um `SUM` `MIN` `MAX` `AVG` imóvel, você deve [incluir o caminho relevante na política de indexação](index-policy.md#includeexclude-strategy).

## <a name="next-steps"></a>Passos seguintes

- [Introdução ao Azure Cosmos DB](introduction.md)
- [Funções de sistema](sql-query-system-functions.md)
- [Funções definidas pelo utilizador](sql-query-udfs.md)