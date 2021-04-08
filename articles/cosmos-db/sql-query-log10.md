---
title: LOG10 em Azure Cosmos DB linguagem de consulta
description: Conheça a função do sistema LOG10 SQL em Azure Cosmos DB para devolver o logaritmo de base-10 da expressão numérica especificada
author: ginamr
ms.service: cosmos-db
ms.subservice: cosmosdb-sql
ms.topic: conceptual
ms.date: 09/13/2019
ms.author: girobins
ms.custom: query-reference
ms.openlocfilehash: 27f7d916c1a3f84f26674fea6b04597fd9e546bc
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/29/2021
ms.locfileid: "93338441"
---
# <a name="log10-azure-cosmos-db"></a>LOG10 (Azure Cosmos DB)
[!INCLUDE[appliesto-sql-api](includes/appliesto-sql-api.md)]

 Devolve o logaritmo base-10 da expressão numérica especificada.  
  
## <a name="syntax"></a>Sintaxe
  
```sql
LOG10 (<numeric_expr>)  
```  
  
## <a name="arguments"></a>Argumentos
  
*numeric_expression*  
   É uma expressão numérica.  
  
## <a name="return-types"></a>Tipos de retorno
  
  Devolve uma expressão numérica.  
  
## <a name="remarks"></a>Observações
  
  As funções LOG10 e POWER estão inversamente relacionadas entre si. Por exemplo, 10 ^ LOG10(n) = n. Esta função do sistema não utilizará o índice.
  
## <a name="examples"></a>Exemplos
  
  O exemplo a seguir declara uma variável e devolve o valor LOG10 da variável especificada (100).  
  
```sql
SELECT LOG10(100) AS log10 
```  
  
 Aqui está o conjunto de resultados.  
  
```json
[{log10: 2}]  
```  

## <a name="next-steps"></a>Passos seguintes

- [Funções matemáticas Azure Cosmos DB](sql-query-mathematical-functions.md)
- [Funciona O sistema Azure Cosmos DB](sql-query-system-functions.md)
- [Introdução ao Azure Cosmos DB](introduction.md)
