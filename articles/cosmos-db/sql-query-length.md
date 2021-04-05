---
title: COMPRIMENTO em Azure Cosmos DB linguagem de consulta
description: Saiba mais sobre a função do sistema SQL LENGTH em Azure Cosmos DB.
author: ginamr
ms.service: cosmos-db
ms.subservice: cosmosdb-sql
ms.topic: conceptual
ms.date: 09/13/2019
ms.author: girobins
ms.custom: query-reference
ms.openlocfilehash: 1d95a92cde25e7582c46c695a43559336466c5f2
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/29/2021
ms.locfileid: "93333686"
---
# <a name="length-azure-cosmos-db"></a>COMPRIMENTO (Azure Cosmos DB)
[!INCLUDE[appliesto-sql-api](includes/appliesto-sql-api.md)]

 Devolve o número de caracteres da expressão de corda especificada.  
  
## <a name="syntax"></a>Sintaxe
  
```sql
LENGTH(<str_expr>)  
```  
  
## <a name="arguments"></a>Argumentos
  
*str_expr*  
   É a expressão de corda a ser avaliada.  
  
## <a name="return-types"></a>Tipos de retorno
  
  Devolve uma expressão numérica.  
  
## <a name="examples"></a>Exemplos
  
  O exemplo a seguir devolve o comprimento de uma corda.  
  
```sql
SELECT LENGTH("abc") AS len 
```  
  
 Aqui está o conjunto de resultados.  
  
```json
[{"len": 3}]  
```  

## <a name="remarks"></a>Observações

Esta função do sistema não utilizará o índice.

## <a name="next-steps"></a>Passos seguintes

- [Funções de corda Azure Cosmos DB](sql-query-string-functions.md)
- [Funciona O sistema Azure Cosmos DB](sql-query-system-functions.md)
- [Introdução ao Azure Cosmos DB](introduction.md)
