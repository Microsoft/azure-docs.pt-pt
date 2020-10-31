---
title: POWER in Azure Cosmos DB linguagem de consulta
description: Saiba mais sobre a função do sistema SQL POWER em Azure Cosmos DB.
author: ginamr
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 09/13/2019
ms.author: girobins
ms.custom: query-reference
ms.openlocfilehash: 41c5cc1a6032a0aee16f1922fc59349449c65b55
ms.sourcegitcommit: 3bdeb546890a740384a8ef383cf915e84bd7e91e
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/30/2020
ms.locfileid: "93091945"
---
# <a name="power-azure-cosmos-db"></a>POWER (Azure Cosmos DB)
[!INCLUDE[appliesto-sql-api](includes/appliesto-sql-api.md)]

 Devolve o valor da expressão especificada à potência especificada.  
  
## <a name="syntax"></a>Sintaxe
  
```sql
POWER (<numeric_expr1>, <numeric_expr2>)  
```  
  
## <a name="arguments"></a>Argumentos
  
*numeric_expr1*  
   É uma expressão numérica.  
  
*numeric_expr2*  
   É o poder para levantar *numeric_expr1* .  
  
## <a name="return-types"></a>Tipos de retorno
  
  Devolve uma expressão numérica.  
  
## <a name="examples"></a>Exemplos
  
  O exemplo a seguir demonstra elevar um número à potência de 3 (o cubo do número).  
  
```sql
SELECT POWER(2, 3) AS pow1, POWER(2.5, 3) AS pow2  
```  
  
 Aqui está o conjunto de resultados.  
  
```json
[{pow1: 8, pow2: 15.625}]  
```  

## <a name="next-steps"></a>Passos seguintes

- [Funções matemáticas Azure Cosmos DB](sql-query-mathematical-functions.md)
- [Funciona O sistema Azure Cosmos DB](sql-query-system-functions.md)
- [Introdução ao Azure Cosmos DB](introduction.md)
