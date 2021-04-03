---
title: Conte em Azure Cosmos DB linguagem de consulta
description: Saiba mais sobre a função do sistema Count (COUNT) SQL em Azure Cosmos DB.
author: timsander1
ms.service: cosmos-db
ms.subservice: cosmosdb-sql
ms.topic: conceptual
ms.date: 12/02/2020
ms.author: tisande
ms.custom: query-reference
ms.openlocfilehash: 5228558f4bcb146ec08ee5fff45fb1bdf4d56f01
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/29/2021
ms.locfileid: "96553403"
---
# <a name="count-azure-cosmos-db"></a>COUNT (Azure Cosmos DB)
[!INCLUDE[appliesto-sql-api](includes/appliesto-sql-api.md)]

Esta função do sistema devolve a contagem dos valores na expressão.
  
## <a name="syntax"></a>Sintaxe
  
```sql
COUNT(<scalar_expr>)  
```  
  
## <a name="arguments"></a>Argumentos
  
*scalar_expr*  
   É qualquer expressão escalar
  
## <a name="return-types"></a>Tipos de retorno
  
Devolve uma expressão numérica.  
  
## <a name="examples"></a>Exemplos
  
O exemplo a seguir devolve a contagem total de artigos num contentor:
  
```sql
SELECT COUNT(1)
FROM c
``` 
O COUNT pode tomar qualquer expressão escalar como entrada. A consulta abaixo produzirá resultados equivalentes:

```sql
SELECT COUNT(2)
FROM c
```

## <a name="remarks"></a>Observações

Esta função do sistema beneficiará de um índice de [gama](index-policy.md#includeexclude-strategy) para quaisquer propriedades no filtro da consulta.

## <a name="next-steps"></a>Passos seguintes

- [Funções matemáticas em Azure Cosmos DB](sql-query-mathematical-functions.md)
- [Funciona o sistema em Azure Cosmos DB](sql-query-system-functions.md)
- [Funções agregadas em Azure Cosmos DB](sql-query-aggregate-functions.md)