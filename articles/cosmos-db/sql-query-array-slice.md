---
title: ARRAY_SLICE na linguagem de consulta DB do Cosmos Azure
description: Saiba como o sistema SQL fatia Array funciona no Azure Cosmos DB devolve parte de uma expressão de matriz
author: ginamr
ms.service: cosmos-db
ms.subservice: cosmosdb-sql
ms.topic: conceptual
ms.date: 03/03/2020
ms.author: girobins
ms.custom: query-reference
ms.openlocfilehash: 1808070f374614ae6cac9a27098ddf2f4d9d2d12
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/29/2021
ms.locfileid: "93332530"
---
# <a name="array_slice-azure-cosmos-db"></a>ARRAY_SLICE (Azure Cosmos DB)
[!INCLUDE[appliesto-sql-api](includes/appliesto-sql-api.md)]

 Devolve parte de uma expressão de matriz.
  
## <a name="syntax"></a>Sintaxe
  
```sql
ARRAY_SLICE (<arr_expr>, <num_expr> [, <num_expr>])  
```  
  
## <a name="arguments"></a>Argumentos
  
*arr_expr*  
   É qualquer expressão de matriz.  
  
*num_expr*  
   Índice numérico baseado em zero para iniciar a matriz. Podem ser utilizados valores negativos para especificar o índice inicial em relação ao último elemento da matriz, ou seja, -1 refere o último elemento da matriz.  

*num_expr* Expressão numérica opcional que define o número máximo de elementos na matriz resultante.    

## <a name="return-types"></a>Tipos de retorno
  
  Devolve uma expressão de matriz.  
  
## <a name="examples"></a>Exemplos
  
  O exemplo a seguir mostra como obter diferentes fatias de uma matriz utilizando `ARRAY_SLICE` .  
  
```sql
SELECT
           ARRAY_SLICE(["apples", "strawberries", "bananas"], 1) AS s1,  
           ARRAY_SLICE(["apples", "strawberries", "bananas"], 1, 1) AS s2,
           ARRAY_SLICE(["apples", "strawberries", "bananas"], -2, 1) AS s3,
           ARRAY_SLICE(["apples", "strawberries", "bananas"], -2, 2) AS s4,
           ARRAY_SLICE(["apples", "strawberries", "bananas"], 1, 0) AS s5,
           ARRAY_SLICE(["apples", "strawberries", "bananas"], 1, 1000) AS s6,
           ARRAY_SLICE(["apples", "strawberries", "bananas"], 1, -100) AS s7      
  
```  
  
 Aqui está o conjunto de resultados.  
  
```json
[{  
           "s1": ["strawberries", "bananas"],   
           "s2": ["strawberries"],
           "s3": ["strawberries"],  
           "s4": ["strawberries", "bananas"], 
           "s5": [],
           "s6": ["strawberries", "bananas"],
           "s7": [] 
}]  
```  

## <a name="remarks"></a>Observações

Esta função do sistema não utilizará o índice.

## <a name="next-steps"></a>Passos seguintes

- [Funções de matriz Azure Cosmos DB](sql-query-array-functions.md)
- [Funciona O sistema Azure Cosmos DB](sql-query-system-functions.md)
- [Introdução ao Azure Cosmos DB](introduction.md)
