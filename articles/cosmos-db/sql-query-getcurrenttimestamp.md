---
title: GetCurrentTimestamp em linguagem de consulta DB Azure Cosmos
description: Saiba mais sobre a função do sistema SQL GetCurrentTimestamp em Azure Cosmos DB.
author: ginamr
ms.service: cosmos-db
ms.subservice: cosmosdb-sql
ms.topic: conceptual
ms.date: 08/19/2020
ms.author: girobins
ms.custom: query-reference
ms.openlocfilehash: 6720b0e5d13f2baaaf063fef2244b0c1f1863571
ms.sourcegitcommit: fa90cd55e341c8201e3789df4cd8bd6fe7c809a3
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 11/04/2020
ms.locfileid: "93341931"
---
# <a name="getcurrenttimestamp-azure-cosmos-db"></a>GetCurrentTimestamp (Azure Cosmos DB)
[!INCLUDE[appliesto-sql-api](includes/appliesto-sql-api.md)]

 Devolve o número de milissegundos que decorreram desde as 00:00 de quinta-feira, 1 de janeiro de 1970.
  
## <a name="syntax"></a>Syntax
  
```sql
GetCurrentTimestamp ()  
```  
  
## <a name="return-types"></a>Tipos de retorno
  
Devolve um valor numérico assinado, o número atual de milissegundos que decorreram desde a época Unix, ou seja, o número de milissegundos que decorreram desde as 00:00:00 quinta-feira, 1 de janeiro de 1970.

## <a name="remarks"></a>Observações

GetCurrentTimestamp é uma função não determinística. O resultado devolvido é UTC (Tempo Universal Coordenado).

Esta função do sistema não utilizará o índice.

## <a name="examples"></a>Exemplos
  
  O exemplo a seguir mostra como obter o tempotamp atual utilizando a função GetCurrentTimestamp() incorporado.
  
```sql
SELECT GetCurrentTimestamp() AS currentUtcTimestamp
```  
  
 Aqui está um conjunto de resultados de exemplo.
  
```json
[{
  "currentUtcTimestamp": 1556916469065
}]  
```

## <a name="next-steps"></a>Passos seguintes

- [Funções de data e hora Azure Cosmos DB](sql-query-date-time-functions.md)
- [Funciona O sistema Azure Cosmos DB](sql-query-system-functions.md)
- [Introdução ao Azure Cosmos DB](introduction.md)
