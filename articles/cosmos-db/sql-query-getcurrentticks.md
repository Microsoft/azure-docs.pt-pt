---
title: GetCurrentTicks em Azure Cosmos DB linguagem de consulta
description: Saiba mais sobre a função do sistema SQL GetCurrentTicks em Azure Cosmos DB.
author: timsander1
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 08/14/2020
ms.author: tisande
ms.custom: query-reference
ms.openlocfilehash: 6b3cd5ab5849c33172e4a629c79fb792b82f1255
ms.sourcegitcommit: 152c522bb5ad64e5c020b466b239cdac040b9377
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 08/14/2020
ms.locfileid: "88227369"
---
# <a name="getcurrentticks-azure-cosmos-db"></a>GetCurrentTicks (Azure Cosmos DB)

Devolve a data e hora atuais, medido em carraças.
  
## <a name="syntax"></a>Sintaxe
  
```sql
GetCurrentTicks ()
```

## <a name="return-types"></a>Tipos de retorno

Devolve um valor inteiro positivo.

## <a name="remarks"></a>Observações

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

## <a name="next-steps"></a>Próximos passos

- [Funções de data e hora Azure Cosmos DB](sql-query-date-time-functions.md)
- [Funciona O sistema Azure Cosmos DB](sql-query-system-functions.md)
- [Introdução ao Azure Cosmos DB](introduction.md)
