---
title: Funções matemáticas na linguagem de consulta DB do Cosmos Azure
description: Conheça as funções matemáticas em Azure Cosmos DB para realizar um cálculo, com base em valores de entrada que são fornecidos como argumentos, e devolva um valor numérico.
author: ginamr
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 09/13/2019
ms.author: girobins
ms.custom: query-reference
ms.openlocfilehash: b6b2c47d5a4cb71ecf46d1eb1c33e2f667fa8052
ms.sourcegitcommit: 3bdeb546890a740384a8ef383cf915e84bd7e91e
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/30/2020
ms.locfileid: "93078259"
---
# <a name="mathematical-functions-azure-cosmos-db"></a>Funções matemáticas (Azure Cosmos DB)  
[!INCLUDE[appliesto-sql-api](includes/appliesto-sql-api.md)]

As funções matemáticas realizam cada um um cálculo, com base em valores de entrada que são fornecidos como argumentos, e devolvem um valor numérico.

Pode executar consultas como o seguinte exemplo:

```sql
    SELECT VALUE ABS(-4)
```

O resultado é:

```json
    [4]
```

## <a name="functions"></a>Funções

As seguintes funções matemáticas incorporadas suportadas executam um cálculo, geralmente baseado em argumentos de entrada, e devolvem uma expressão numérica:
 
* [ABS](sql-query-abs.md)
* [ACOS](sql-query-acos.md)
* [ASIN](sql-query-asin.md)
* [ATAN](sql-query-atan.md)
* [ATN2](sql-query-atn2.md)
* [CEILING](sql-query-ceiling.md)
* [COS](sql-query-cos.md)
* [COT](sql-query-cot.md)
* [DEGREES](sql-query-degrees.md)
* [EXP](sql-query-exp.md)
* [FLOOR](sql-query-floor.md)
* [LOG](sql-query-log.md)
* [LOG10](sql-query-log10.md)
* [PI](sql-query-pi.md)
* [POWER](sql-query-power.md)
* [RADIANS](sql-query-radians.md)
* [RAND](sql-query-rand.md)
* [ROUND](sql-query-round.md)
* [SIGN](sql-query-sign.md)
* [SIN](sql-query-sin.md)
* [SQRT](sql-query-sqrt.md)
* [PRAÇA](sql-query-square.md)
* [TAN](sql-query-tan.md)
* [TRUNC](sql-query-trunc.md)

  
Todas as funções matemáticas, exceto a RAND, são funções determinísticas. Isto significa que devolvem os mesmos resultados cada vez que são chamados com um conjunto específico de valores de entrada.

## <a name="next-steps"></a>Passos seguintes

- [Funciona O sistema Azure Cosmos DB](sql-query-system-functions.md)
- [Introdução ao Azure Cosmos DB](introduction.md)
- [Funções definidas pelo utilizador](sql-query-udfs.md)
- [Agregados](sql-query-aggregates.md)
