---
title: CONTÉM em linguagem de consulta de Azure Cosmos DB
description: Saiba como o sistema CONTAINS SQL funciona em Azure Cosmos DB devolve uma Boolean indicando se a primeira expressão de corda contém a segunda
author: ginamr
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 03/03/2020
ms.author: girobins
ms.custom: query-reference
ms.openlocfilehash: c0c25b63fb6a7bf42bd2ec5b9503cac2cce7583f
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/28/2020
ms.locfileid: "78302598"
---
# <a name="contains-azure-cosmos-db"></a>CONTÉM (Azure Cosmos DB)
 Devolve uma Boolean indicando se a primeira expressão de corda contém a segunda.  
  
## <a name="syntax"></a>Sintaxe
  
```sql
CONTAINS(<str_expr1>, <str_expr2>)  
```  
  
## <a name="arguments"></a>Argumentos
  
*str_expr1*  
   É a expressão de corda a ser revistada.  
  
*str_expr2*  
   É a expressão de corda para encontrar.  
  
## <a name="return-types"></a>Tipos de retorno
  
  Devolve uma expressão booleana.  
  
## <a name="examples"></a>Exemplos
  
  Verifica-se se "abc" contém "ab" e se "abc" contém "d".  
  
```sql
SELECT CONTAINS("abc", "ab") AS c1, CONTAINS("abc", "d") AS c2 
```  
  
 Aqui está o conjunto de resultados.  
  
```json
[{"c1": true, "c2": false}]  
```  

## <a name="remarks"></a>Observações

Esta função do sistema não utilizará o índice.

## <a name="next-steps"></a>Passos seguintes

- [Funções de corda Azure Cosmos DB](sql-query-string-functions.md)
- [Funcionamento do sistema Azure Cosmos DB](sql-query-system-functions.md)
- [Introdução ao Azure Cosmos DB](introduction.md)
