---
title: ARRAY_LENGTH na linguagem de consulta DB do Azure Cosmos
description: Saiba como o sistema SQL do comprimento array funciona no Azure Cosmos DB devolve o número de elementos da expressão de matriz especificada
author: ginamr
ms.service: cosmos-db
ms.subservice: cosmosdb-sql
ms.topic: conceptual
ms.date: 03/03/2020
ms.author: girobins
ms.custom: query-reference
ms.openlocfilehash: 014dc2584b82951f575468ef31ee2ed69a8ef753
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/29/2021
ms.locfileid: "93332598"
---
# <a name="array_length-azure-cosmos-db"></a>ARRAY_LENGTH (Azure Cosmos DB)
[!INCLUDE[appliesto-sql-api](includes/appliesto-sql-api.md)]

 Devolve o número de elementos da expressão de matriz especificada.  
  
## <a name="syntax"></a>Sintaxe
  
```sql
ARRAY_LENGTH(<arr_expr>)  
```  
  
## <a name="arguments"></a>Argumentos
  
*arr_expr*  
   É uma expressão de matriz.  
  
## <a name="return-types"></a>Tipos de retorno
  
  Devolve uma expressão numérica.  
  
## <a name="examples"></a>Exemplos
  
  O exemplo a seguir como obter o comprimento de uma matriz utilizando `ARRAY_LENGTH` .  
  
```sql
SELECT ARRAY_LENGTH(["apples", "strawberries", "bananas"]) AS len  
```  
  
 Aqui está o conjunto de resultados.  
  
```json
[{"len": 3}]  
```  
  
## <a name="remarks"></a>Observações

Esta função do sistema não utilizará o índice.

## <a name="next-steps"></a>Passos seguintes

- [Funções de matriz Azure Cosmos DB](sql-query-array-functions.md)
- [Funciona O sistema Azure Cosmos DB](sql-query-system-functions.md)
- [Introdução ao Azure Cosmos DB](introduction.md)
