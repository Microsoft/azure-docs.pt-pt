---
title: ARRAY_CONCAT na linguagem de consulta Azure Cosmos DB
description: Saiba como a função de sistema SQL Concat da matriz em Azure Cosmos DB retorna uma matriz que é o resultado da concatenação de dois ou mais valores de matriz
author: ginamr
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 09/13/2019
ms.author: girobins
ms.custom: query-reference
ms.openlocfilehash: 10370e16c95f4fc747dd3a66a56794da38562972
ms.sourcegitcommit: 9405aad7e39efbd8fef6d0a3c8988c6bf8de94eb
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 12/05/2019
ms.locfileid: "74871827"
---
# <a name="array_concat-azure-cosmos-db"></a>ARRAY_CONCAT (Azure Cosmos DB)
 Devolve uma matriz que é o resultado da concatenação de dois ou mais valores de matriz.  
  
## <a name="syntax"></a>Sintaxe
  
```sql
ARRAY_CONCAT (<arr_expr1>, <arr_expr2> [, <arr_exprN>])  
```  
  
## <a name="arguments"></a>Argumentos
  
*arr_expr*  
   É uma expressão de matriz para concatenar com os outros valores. A função `ARRAY_CONCAT` requer pelo menos dois argumentos de *arr_expr* .  
  
## <a name="return-types"></a>Tipos de retorno
  
  Devolve uma expressão de matriz.  
  
## <a name="examples"></a>Exemplos
  
  O exemplo a seguir como concatenar duas matrizes.  
  
```sql
SELECT ARRAY_CONCAT(["apples", "strawberries"], ["bananas"]) AS arrayConcat 
```  
  
 Aqui está o conjunto de resultados.  
  
```json
[{"arrayConcat": ["apples", "strawberries", "bananas"]}]  
```  
  

## <a name="next-steps"></a>Passos seguintes

- [Funções de matriz Azure Cosmos DB](sql-query-array-functions.md)
- [Funções do sistema Azure Cosmos DB](sql-query-system-functions.md)
- [Introdução ao Azure Cosmos DB](introduction.md)
