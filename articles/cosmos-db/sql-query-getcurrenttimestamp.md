---
title: GetCurrentTimestamp na linguagem de consulta do Azure Cosmos DB
description: Saiba mais sobre a função do sistema SQL GetCurrentTimestamp em Azure Cosmos DB.
author: ginamr
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 09/13/2019
ms.author: girobins
ms.custom: query-reference
ms.openlocfilehash: b816c63da56025fe6e9cbaece2cde5dcd01585d4
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/27/2020
ms.locfileid: "71351003"
---
# <a name="getcurrenttimestamp-azure-cosmos-db"></a>GetCurrentTimestamp (Azure Cosmos DB)
 Devolve o número de milissegundos que decorreram desde as 00:00:00 de quinta-feira, 1 de janeiro de 1970. 
  
## <a name="syntax"></a>Sintaxe
  
```sql
GetCurrentTimestamp ()  
```  
  
## <a name="return-types"></a>Tipos de retorno
  
  Devolve um valor numérico, o número atual de milissegundos que decorreram desde a época Unix, ou seja, o número de milissegundos que decorreram desde as 00:00:00 de quinta-feira, 1 de janeiro de 1970.

## <a name="remarks"></a>Observações

  GetCurrentTimestamp é uma função não determinística.
  
  O resultado devolvido é UTC (Tempo Universal Coordenado).

## <a name="examples"></a>Exemplos
  
  O exemplo seguinte mostra como obter a marca de tempo atual usando a função GetCurrentTimestamp() incorporada.
  
```sql
SELECT GetCurrentTimestamp() AS currentUtcTimestamp
```  
  
 Aqui está um exemplo de resultadodefinido.
  
```json
[{
  "currentUtcTimestamp": 1556916469065
}]  
```

## <a name="next-steps"></a>Passos seguintes

- [Funções de data e hora Azure Cosmos DB](sql-query-date-time-functions.md)
- [Funcionamento do sistema Azure Cosmos DB](sql-query-system-functions.md)
- [Introdução ao Azure Cosmos DB](introduction.md)
