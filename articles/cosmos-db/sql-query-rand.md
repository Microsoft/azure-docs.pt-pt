---
title: RAND em Azure Cosmos DB linguagem de consulta
description: Saiba mais sobre a função do sistema SQL RAND em Azure Cosmos DB.
author: ginamr
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 09/16/2019
ms.author: girobins
ms.custom: query-reference
ms.openlocfilehash: 3e287f119a67c8bb00a309833e2ad6fe0d88975a
ms.sourcegitcommit: 3bdeb546890a740384a8ef383cf915e84bd7e91e
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/30/2020
ms.locfileid: "93076100"
---
# <a name="rand-azure-cosmos-db"></a>RAND (Azure Cosmos DB)
[!INCLUDE[appliesto-sql-api](includes/appliesto-sql-api.md)]

 Devolve um valor numérico gerado aleatoriamente a partir de [0,1).
 
## <a name="syntax"></a>Syntax
  
```sql
RAND ()  
```  

## <a name="return-types"></a>Tipos de retorno

  Devolve uma expressão numérica.

## <a name="remarks"></a>Observações

  `RAND` é uma função não dedeterminística. Chamadas repetitivas de `RAND` não devolver os mesmos resultados. Esta função do sistema não utilizará o índice.


## <a name="examples"></a>Exemplos
  
  O exemplo a seguir devolve um valor numérico gerado aleatoriamente.
  
```sql
SELECT RAND() AS rand 
```  
  
 Aqui está o conjunto de resultados.  
  
```json
[{"rand": 0.87860053195618093}]  
``` 

## <a name="next-steps"></a>Passos seguintes

- [Funções matemáticas Azure Cosmos DB](sql-query-mathematical-functions.md)
- [Funciona O sistema Azure Cosmos DB](sql-query-system-functions.md)
- [Introdução ao Azure Cosmos DB](introduction.md)
