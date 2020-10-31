---
title: REPLICAR em Azure Cosmos DB linguagem de consulta
description: Saiba mais sobre a função do sistema SQL REPLICA em Azure Cosmos DB.
author: ginamr
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 03/03/2020
ms.author: girobins
ms.custom: query-reference
ms.openlocfilehash: c8b1fc1cb28b62a388df53238df58420bd3317a8
ms.sourcegitcommit: 3bdeb546890a740384a8ef383cf915e84bd7e91e
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/30/2020
ms.locfileid: "93082866"
---
# <a name="replicate-azure-cosmos-db"></a>REPLICA (Azure Cosmos DB)
[!INCLUDE[appliesto-sql-api](includes/appliesto-sql-api.md)]

 Repete um valor de cadeia um número especificado de vezes.
  
## <a name="syntax"></a>Sintaxe
  
```sql
REPLICATE(<str_expr>, <num_expr>)
```  
  
## <a name="arguments"></a>Argumentos
  
*str_expr*  
   É uma expressão de corda.
  
*num_expr*  
   É uma expressão numérica. Se *num_expr* for negativo ou não finito, o resultado é indefinido.
  
## <a name="return-types"></a>Tipos de retorno
  
  Devolve uma expressão de corda.
  
## <a name="remarks"></a>Observações

  O comprimento máximo do resultado é de 10.000 caracteres, ou seja, (comprimento *str_expr* *  *num_expr* ) <= 10.000. Esta função do sistema não utilizará o índice.

## <a name="examples"></a>Exemplos
  
  O exemplo a seguir mostra como se usa `REPLICATE` numa consulta.
  
```sql
SELECT REPLICATE("a", 3) AS replicate
```  
  
 Aqui está o conjunto de resultados.
  
```json
[{"replicate": "aaa"}]
```  

## <a name="next-steps"></a>Passos seguintes

- [Funções de corda Azure Cosmos DB](sql-query-string-functions.md)
- [Funciona O sistema Azure Cosmos DB](sql-query-system-functions.md)
- [Introdução ao Azure Cosmos DB](introduction.md)
