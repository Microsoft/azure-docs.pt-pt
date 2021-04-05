---
title: SUM em Azure Cosmos DB linguagem de consulta
description: Saiba mais sobre a função do sistema SQL Sum (SUM) em Azure Cosmos DB.
author: timsander1
ms.service: cosmos-db
ms.subservice: cosmosdb-sql
ms.topic: conceptual
ms.date: 12/02/2020
ms.author: tisande
ms.custom: query-reference
ms.openlocfilehash: d5a86cd5af504072480e0cd749caa6c0532d0bc1
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/29/2021
ms.locfileid: "96553427"
---
# <a name="sum-azure-cosmos-db"></a>SUM (Azure Cosmos DB)
[!INCLUDE[appliesto-sql-api](includes/appliesto-sql-api.md)]

Esta função agregada devolve a soma dos valores na expressão.
  
## <a name="syntax"></a>Sintaxe
  
```sql
SUM(<numeric_expr>)  
```  
  
## <a name="arguments"></a>Argumentos
  
*numeric_expr*  
   É uma expressão numérica.  
  
## <a name="return-types"></a>Tipos de retorno
  
Devolve uma expressão numérica.  
  
## <a name="examples"></a>Exemplos
  
O exemplo a seguir devolve a soma `propertyA` de:
  
```sql
SELECT SUM(c.propertyA)
FROM c
```  

## <a name="remarks"></a>Observações

Esta função do sistema beneficiará de um [índice de intervalo.](index-policy.md#includeexclude-strategy) Se quaisquer argumentos `SUM` forem string, boolean ou nulos, toda a função agregada do sistema regressará `undefined` . Se algum argumento tiver um `undefined` valor, não terá impacto no `SUM` cálculo.

## <a name="next-steps"></a>Passos seguintes

- [Funções matemáticas em Azure Cosmos DB](sql-query-mathematical-functions.md)
- [Funciona o sistema em Azure Cosmos DB](sql-query-system-functions.md)
- [Funções agregadas em Azure Cosmos DB](sql-query-aggregate-functions.md)