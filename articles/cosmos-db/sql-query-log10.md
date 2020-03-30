---
title: LOG10 em linguagem de consulta do Azure Cosmos DB
description: Saiba mais sobre a função do sistema LOG10 SQL em Azure Cosmos DB para devolver o logarithm base-10 da expressão numérica especificada
author: ginamr
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 09/13/2019
ms.author: girobins
ms.custom: query-reference
ms.openlocfilehash: 6f47200f6978d91f46c010640bb9c2bb26e9b7d5
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/28/2020
ms.locfileid: "78302496"
---
# <a name="log10-azure-cosmos-db"></a>LOG10 (Azure Cosmos DB)
 Devolve a base-10 logarithm da expressão numérica especificada.  
  
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
  
  As funções LOG10 e POWER estão inversamente relacionadas entre si. Por exemplo, 10 ^ LOG10(n) = n.  
  
## <a name="examples"></a>Exemplos
  
  O exemplo seguinte declara uma variável e devolve o valor LOG10 da variável especificada (100).  
  
```sql
SELECT LOG10(100) AS log10 
```  
  
 Aqui está o conjunto de resultados.  
  
```json
[{log10: 2}]  
```  

## <a name="remarks"></a>Observações

Esta função do sistema não utilizará o índice.

## <a name="next-steps"></a>Passos seguintes

- [Funções matemáticas Azure Cosmos DB](sql-query-mathematical-functions.md)
- [Funcionamento do sistema Azure Cosmos DB](sql-query-system-functions.md)
- [Introdução ao Azure Cosmos DB](introduction.md)
