---
title: ARRAY_SLICE na linguagem de consulta Azure Cosmos DB
description: Saiba mais sobre como a função de sistema SQL de fatia de matriz em Azure Cosmos DB retorna parte de uma expressão de matriz
author: ginamr
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 09/13/2019
ms.author: girobins
ms.custom: query-reference
ms.openlocfilehash: 3c65886eabfa87f729e18fb854aca6662d1d6fac
ms.sourcegitcommit: 9405aad7e39efbd8fef6d0a3c8988c6bf8de94eb
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 12/05/2019
ms.locfileid: "74871793"
---
# <a name="array_slice-azure-cosmos-db"></a>ARRAY_SLICE (Azure Cosmos DB)
 Devolve a parte de uma expressão de matriz.
  
## <a name="syntax"></a>Sintaxe
  
```sql
ARRAY_SLICE (<arr_expr>, <num_expr> [, <num_expr>])  
```  
  
## <a name="arguments"></a>Argumentos
  
*arr_expr*  
   É qualquer expressão de matriz.  
  
*num_expr*  
   Índice numérico baseado em zero na qual deve começar a matriz. Valores negativos podem ser utilizados para especificar o índice de início em relação ao último elemento de matriz, ou seja, -1 referencia o último elemento na matriz.  

*num_expr* Expressão numérica opcional que define o número máximo de elementos na matriz resultante.    

## <a name="return-types"></a>Tipos de retorno
  
  Devolve uma expressão de matriz.  
  
## <a name="examples"></a>Exemplos
  
  O exemplo a seguir mostra como obter diferentes fatias de uma matriz usando `ARRAY_SLICE`.  
  
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

## <a name="next-steps"></a>Passos seguintes

- [Funções de matriz Azure Cosmos DB](sql-query-array-functions.md)
- [Funções do sistema Azure Cosmos DB](sql-query-system-functions.md)
- [Introdução ao Azure Cosmos DB](introduction.md)
