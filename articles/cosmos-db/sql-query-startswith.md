---
title: STARTWITH em linguagem de consulta de Azure Cosmos DB
description: Saiba mais sobre a função do sistema SQL STARTWITH em Azure Cosmos DB.
author: ginamr
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 09/13/2019
ms.author: girobins
ms.custom: query-reference
ms.openlocfilehash: 9ed49c067946186f8b79f67bad0a460113eacb73
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/28/2020
ms.locfileid: "78295713"
---
# <a name="startswith-azure-cosmos-db"></a>STARTWITH (Azure Cosmos DB)
 Devolve uma Boolean indicando se a primeira expressão de corda começa com a segunda.  
  
## <a name="syntax"></a>Sintaxe
  
```sql
STARTSWITH(<str_expr1>, <str_expr2>)  
```  
  
## <a name="arguments"></a>Argumentos
  
*str_expr1*  
   É uma expressão de cordas.
  
*str_expr2*  
   É uma expressão de corda a ser comparada com o início de *str_expr1*.

## <a name="return-types"></a>Tipos de retorno
  
  Devolve uma expressão booleana.  
  
## <a name="examples"></a>Exemplos
  
  O exemplo seguinte verifica se a corda "abc" começa com "b" e "a".  
  
```sql
SELECT STARTSWITH("abc", "b") AS s1, STARTSWITH("abc", "a") AS s2  
```  
  
 Aqui está o conjunto de resultados.  
  
```json
[{"s1": false, "s2": true}]  
```  

## <a name="remarks"></a>Observações

Esta função do sistema beneficiará de um índice de [alcance](index-policy.md#includeexclude-strategy).

## <a name="next-steps"></a>Passos seguintes

- [Funções de corda Azure Cosmos DB](sql-query-string-functions.md)
- [Funcionamento do sistema Azure Cosmos DB](sql-query-system-functions.md)
- [Introdução ao Azure Cosmos DB](introduction.md)
