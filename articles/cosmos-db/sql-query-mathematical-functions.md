---
title: Funções matemáticas na linguagem de consulta Azure Cosmos DB
description: Saiba mais sobre as funções matemáticas no Azure Cosmos DB para executar um cálculo, com base nos valores de entrada fornecidos como argumentos, e retornar um valor numérico.
author: ginamr
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 09/13/2019
ms.author: girobins
ms.custom: query-reference
ms.openlocfilehash: a04867d356aaf2f55dbe900d2e35b42f74206851
ms.sourcegitcommit: 9405aad7e39efbd8fef6d0a3c8988c6bf8de94eb
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 12/05/2019
ms.locfileid: "74873272"
---
# <a name="mathematical-functions-azure-cosmos-db"></a>Funções matemáticas (Azure Cosmos DB)  

As funções matemáticas realizar um cálculo, com base nos valores de entrada que são fornecidos como argumentos e devolvem um valor numérico.

Você pode executar consultas como o exemplo a seguir:

```sql
    SELECT VALUE ABS(-4)
```

O resultado é:

```json
    [4]
```

## <a name="functions"></a>Funções

As seguintes funções matemáticas internas com suporte executam um cálculo, geralmente com base em argumentos de entrada e retornam uma expressão numérica.
  
||||  
|-|-|-|  
|[ABS](sql-query-abs.md)|[ACOS](sql-query-acos.md)|[ASIN](sql-query-asin.md)|  
|[ATAN](sql-query-atan.md)|[ATN2](sql-query-atn2.md)|[LIMITE](sql-query-ceiling.md)|  
|[COS](sql-query-cos.md)|[COT](sql-query-cot.md)|[GRAUS](sql-query-degrees.md)|  
|[EXP](sql-query-exp.md)|[FLOOR](sql-query-floor.md)|[LOG](sql-query-log.md)|  
|[LOG10](sql-query-log10.md)|[PI](sql-query-pi.md)|[POWER](sql-query-power.md)|  
|[RADIANOS](sql-query-radians.md)|[RAND](sql-query-rand.md)|[ARREDONDAR](sql-query-round.md)|
|[INÍCIO DE SESSÃO](sql-query-sign.md)|[SIN](sql-query-sin.md)|[SQRT](sql-query-sqrt.md)|
|[SQUARE](sql-query-square.md)|[TAN](sql-query-tan.md)|[TRUNC](sql-query-trunc.md)||  
  
Todas as funções matemáticas, exceto para RAND, são funções determinísticas. Isso significa que eles retornam os mesmos resultados cada vez que são chamados com um conjunto específico de valores de entrada.

## <a name="next-steps"></a>Passos seguintes

- [Funções do sistema Azure Cosmos DB](sql-query-system-functions.md)
- [Introdução ao Azure Cosmos DB](introduction.md)
- [Funções definidas pelo usuário](sql-query-udfs.md)
- [Agregados](sql-query-aggregates.md)
