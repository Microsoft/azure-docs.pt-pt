---
title: GetCurrentTicks em Azure Cosmos DB linguagem de consulta
description: Saiba mais sobre a função do sistema SQL GetCurrentTicks em Azure Cosmos DB.
author: timsander1
ms.service: cosmos-db
ms.subservice: cosmosdb-sql
ms.topic: conceptual
ms.date: 02/03/2021
ms.author: tisande
ms.custom: query-reference
ms.openlocfilehash: 16004e6e471094c99229c32a63396ac3b0490905
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/19/2021
ms.locfileid: "99524310"
---
# <a name="getcurrentticks-azure-cosmos-db"></a>GetCurrentTicks (Azure Cosmos DB)
[!INCLUDE[appliesto-sql-api](includes/appliesto-sql-api.md)]

Devolve o número de carrapatos de 100-nanosegundos que decorreram desde as 00:00 de quinta-feira, 1 de janeiro de 1970.
  
## <a name="syntax"></a>Syntax
  
```sql
GetCurrentTicks ()
```

## <a name="return-types"></a>Tipos de retorno

Devolve um valor numérico assinado, o número atual de carrapatos de 100 nanosegundos que decorreram desde a época Unix. Por outras palavras, a GetCurrentTicks devolve o número de 100 tiques nanosegundos que decorreram desde as 00:00:00 de quinta-feira, 1 de janeiro de 1970.

## <a name="remarks"></a>Observações

GetCurrentTicks é uma função não determinística. O resultado devolvido é UTC (Tempo Universal Coordenado).

> [!NOTE]
> Esta função do sistema não utilizará o índice. Se precisar de comparar valores com o tempo atual, obtenha o tempo atual antes da execução da consulta e use esse valor de cadeia constante na `WHERE` cláusula.

## <a name="examples"></a>Exemplos

Aqui está um exemplo que devolve o tempo atual, medido em carrapatos:

```sql
SELECT GetCurrentTicks() AS CurrentTimeInTicks
```

```json
[
    {
        "CurrentTimeInTicks": 15973607943002652
    }
]
```

## <a name="next-steps"></a>Passos seguintes

- [Funções de data e hora Azure Cosmos DB](sql-query-date-time-functions.md)
- [Funciona O sistema Azure Cosmos DB](sql-query-system-functions.md)
- [Introdução ao Azure Cosmos DB](introduction.md)
