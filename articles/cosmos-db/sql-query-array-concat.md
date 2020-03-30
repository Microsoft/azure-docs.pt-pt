---
title: ARRAY_CONCAT na linguagem de consulta do Azure Cosmos DB
description: Saiba como funciona o sistema Array Concat SQL em Azure Cosmos DB devolve uma matriz que é o resultado da concatenação de dois ou mais valores de matriz
author: ginamr
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 03/03/2020
ms.author: girobins
ms.custom: query-reference
ms.openlocfilehash: 8f2b37181e5d743809bb1f60be4056cb4442a8d2
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/28/2020
ms.locfileid: "78295883"
---
# <a name="array_concat-azure-cosmos-db"></a>ARRAY_CONCAT (Azure Cosmos DB)
 Devolve uma matriz que é o resultado da concatenação de dois ou mais valores de matriz.  
  
## <a name="syntax"></a>Sintaxe
  
```sql
ARRAY_CONCAT (<arr_expr1>, <arr_expr2> [, <arr_exprN>])  
```  
  
## <a name="arguments"></a>Argumentos
  
*arr_expr*  
   É uma expressão matriz para concatenar aos outros valores. A `ARRAY_CONCAT` função requer pelo menos dois *argumentos arr_expr.*  
  
## <a name="return-types"></a>Tipos de retorno
  
  Devolve uma expressão de matriz.  
  
## <a name="examples"></a>Exemplos
  
  O exemplo seguinte como concatenar duas matrizes.  
  
```sql
SELECT ARRAY_CONCAT(["apples", "strawberries"], ["bananas"]) AS arrayConcat 
```  
  
 Aqui está o conjunto de resultados.  
  
```json
[{"arrayConcat": ["apples", "strawberries", "bananas"]}]  
```  
  
## <a name="remarks"></a>Observações

Esta função do sistema não utilizará o índice.

## <a name="next-steps"></a>Passos seguintes

- [Funções de matriz Azure Cosmos DB](sql-query-array-functions.md)
- [Funcionamento do sistema Azure Cosmos DB](sql-query-system-functions.md)
- [Introdução ao Azure Cosmos DB](introduction.md)
