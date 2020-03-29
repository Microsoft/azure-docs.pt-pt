---
title: Funções matemáticas na linguagem de consulta do Azure Cosmos DB
description: Aprenda sobre as funções matemáticas em Azure Cosmos DB para realizar um cálculo, com base em valores de entrada que são fornecidos como argumentos, e devolver um valor numérico.
author: ginamr
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 09/13/2019
ms.author: girobins
ms.custom: query-reference
ms.openlocfilehash: a04867d356aaf2f55dbe900d2e35b42f74206851
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/27/2020
ms.locfileid: "74873272"
---
# <a name="mathematical-functions-azure-cosmos-db"></a>Funções matemáticas (Azure Cosmos DB)  

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

As seguintes funções matemáticas incorporadas suportadas executam um cálculo, geralmente baseado em argumentos de entrada, e devolvem uma expressão numérica.
  
||||  
|-|-|-|  
|[ABDOMINAIS](sql-query-abs.md)|[ACOS](sql-query-acos.md)|[ASIN](sql-query-asin.md)|  
|[ATAN](sql-query-atan.md)|[ATN2](sql-query-atn2.md)|[TETO](sql-query-ceiling.md)|  
|[COS](sql-query-cos.md)|[COT](sql-query-cot.md)|[DEGREES](sql-query-degrees.md)|  
|[EXP](sql-query-exp.md)|[PISO](sql-query-floor.md)|[LOG](sql-query-log.md)|  
|[LOG10](sql-query-log10.md)|[PI](sql-query-pi.md)|[POWER](sql-query-power.md)|  
|[RADIANS](sql-query-radians.md)|[RAND](sql-query-rand.md)|[REDONDO](sql-query-round.md)|
|[SIGN](sql-query-sign.md)|[SIN](sql-query-sin.md)|[SQRT](sql-query-sqrt.md)|
|[SQUARE](sql-query-square.md)|[TAN](sql-query-tan.md)|[TRUNC](sql-query-trunc.md)||  
  
Todas as funções matemáticas, exceto a RAND, são funções determinísticas. Isto significa que devolvem os mesmos resultados sempre que são chamados com um conjunto específico de valores de entrada.

## <a name="next-steps"></a>Passos seguintes

- [Funcionamento do sistema Azure Cosmos DB](sql-query-system-functions.md)
- [Introdução ao Azure Cosmos DB](introduction.md)
- [Funções Definidas pelo Utilizador](sql-query-udfs.md)
- [Agregados](sql-query-aggregates.md)
