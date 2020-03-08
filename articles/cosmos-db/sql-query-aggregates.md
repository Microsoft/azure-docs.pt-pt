---
title: Funções agregadas em Azure Cosmos DB
description: Conheça a sintaxe de função agregada SQL, tipos de funções agregadas suportadas pela Azure Cosmos DB.
author: timsander1
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 03/05/2020
ms.author: tisande
ms.openlocfilehash: df9700dd51c8915ff28c34cf0a29c2f5e48baa44
ms.sourcegitcommit: 668b3480cb637c53534642adcee95d687578769a
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/07/2020
ms.locfileid: "78897835"
---
# <a name="aggregate-functions-in-azure-cosmos-db"></a>Funções agregadas em Azure Cosmos DB

As funções agregadas realizam um cálculo sobre um conjunto de valores na cláusula SELECT e devolvem um único valor. Por exemplo, a seguinte consulta devolve a contagem de artigos dentro do recipiente `Families`:

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

Também pode devolver apenas o valor escalar do agregado utilizando a palavra-chave VALUE. Por exemplo, a consulta seguinte devolve a contagem de valores como um único número:

```sql
    SELECT VALUE COUNT(1)
    FROM Families f
```

Os resultados são:

```json
    [ 2 ]
```

Também pode combinar agregações com filtros. Por exemplo, a seguinte consulta devolve a contagem de itens com o estado de endereço de `WA`.

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

A API SQL suporta as seguintes funções agregadas. A Soma e a AVG operam em valores numéricos, e COUNT, MIN e MAX trabalham em números, cordas, booleans e nulos.

| Função | Descrição |
|-------|-------------|
| COUNT | Devolve o número de itens na expressão. |
| SUM   | Devolve a soma de todos os valores na expressão. |
| MIN   | Devolve o valor mínimo na expressão. |
| MAX   | Devolve o valor máximo na expressão. |
| AVG   | Devolve a média dos valores existentes na expressão. |

Também pode agregar os resultados de uma iteração de matriz.

> [!NOTE]
> No Data Explorer do portal Azure, as consultas de agregação podem agregar resultados parciais em apenas uma página de consulta. O SDK produz um único valor cumulativo em todas as páginas. Para efetuar consultas de agregação utilizando código, necessita de .NET SDK 1.12.0, .NET Core SDK 1.1.0 ou Java SDK 1.9.5 ou superior.

## <a name="next-steps"></a>Passos seguintes

- [Introdução ao Azure Cosmos DB](introduction.md)
- [Funções do sistema](sql-query-system-functions.md)
- [Funções definidas pelo utilizador](sql-query-udfs.md)