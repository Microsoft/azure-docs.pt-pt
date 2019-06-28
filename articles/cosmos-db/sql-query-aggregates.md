---
title: Funções de agregação no Azure Cosmos DB
description: Saiba mais sobre a sintaxe da função de agregação de SQL para o Azure Cosmos DB.
author: markjbrown
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 05/31/2019
ms.author: mjbrown
ms.openlocfilehash: a6937e9e811ea8e44eda6f2bcb5d2c7d78db4934
ms.sourcegitcommit: a12b2c2599134e32a910921861d4805e21320159
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 06/24/2019
ms.locfileid: "67342868"
---
# <a name="aggregate-functions-in-azure-cosmos-db"></a>Funções de agregação no Azure Cosmos DB

As funções de agregação executam um cálculo num conjunto de valores na cláusula SELECT e devolvem um valor único. Por exemplo, a seguinte consulta devolve a contagem de itens dentro do `Families` contentor:

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

Também podem retornar apenas o valor escalar da agregação utilizando a palavra-chave de valor. Por exemplo, a consulta seguinte devolve a contagem de valores como um único número:

```sql
    SELECT VALUE COUNT(1)
    FROM Families f
```

Os resultados são:

```json
    [ 2 ]
```

Também pode combinar as agregações com filtros. Por exemplo, a consulta seguinte devolve a contagem de itens com o estado de endereço de `WA`.

```sql
    SELECT VALUE COUNT(1)
    FROM Families f
    WHERE f.address.state = "WA"
```

Os resultados são:

```json
    [ 1 ]
```

## <a name="types-of-aggregate-functions"></a>Tipos de funções de agregação

A API de SQL suporta as seguintes funções de agregação. SOMA e média utilizar valores numéricos e COUNT, MIN e MAX trabalham em números, cadeias de caracteres, booleanos e nulos.

| Função | Descrição |
|-------|-------------|
| COUNT | Devolve o número de itens na expressão. |
| SUM   | Devolve a soma de todos os valores na expressão. |
| MIN   | Devolve o valor mínimo na expressão. |
| MAX   | Devolve o valor máximo na expressão. |
| AVG   | Devolve a média dos valores existentes na expressão. |

Também pode agregar nos resultados de uma iteração de matriz.

> [!NOTE]
> No Explorador de dados do portal do Azure, as consultas de agregação podem agregar resultados parciais ao longo da página de apenas uma consulta. O SDK produz um valor de cumulativo único em todas as páginas. Para executar consultas de agregação com o código, é necessário o SDK de .NET 1.12.0, o SDK para .NET Core 1.1.0 ou o SDK de Java 1.9.5 ou superior.

## <a name="next-steps"></a>Passos Seguintes

- [Introdução ao Azure Cosmos DB](introduction.md)
- [Funções de sistema](sql-query-system-functions.md)
- [Funções definidas pelo utilizador](sql-query-udfs.md)