---
title: GetCurrentTicks em Azure Cosmos DB linguagem de consulta
description: Saiba mais sobre a função do sistema SQL GetCurrentTicks em Azure Cosmos DB.
author: timsander1
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 08/14/2020
ms.author: tisande
ms.custom: query-reference
ms.openlocfilehash: 2ca76d75edba6688dbe93f11a51a0ad67942677a
ms.sourcegitcommit: d661149f8db075800242bef070ea30f82448981e
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 08/19/2020
ms.locfileid: "88606946"
---
# <a name="getcurrentticks-azure-cosmos-db"></a>GetCurrentTicks (Azure Cosmos DB)

Devolve o número de carrapatos de 100-nanosegundos que decorreram desde as 00:00 de quinta-feira, 1 de janeiro de 1970.
  
## <a name="syntax"></a>Syntax
  
```sql
GetCurrentTicks ()
```

## <a name="return-types"></a>Tipos de retorno

Devolve um valor numérico assinado, o número atual de carrapatos de 100 nanosegundos que decorreram desde a época Unix. Por outras palavras, a GetCurrentTicks devolve o número de 100 tiques nanosegundos que decorreram desde as 00:00:00 de quinta-feira, 1 de janeiro de 1970.

## <a name="remarks"></a>Observações

GetCurrentTicks é uma função não determinística. O resultado devolvido é UTC (Tempo Universal Coordenado).

Esta função do sistema não utilizará o índice.

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
