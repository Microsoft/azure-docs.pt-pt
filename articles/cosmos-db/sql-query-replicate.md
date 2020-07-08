---
title: REPLICAR em Azure Cosmos DB linguagem de consulta
description: Saiba mais sobre a função do sistema SQL REPLICA em Azure Cosmos DB.
author: ginamr
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 03/03/2020
ms.author: girobins
ms.custom: query-reference
ms.openlocfilehash: 19fcde522c5cb0355e53a5616145f27fada7dad9
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.contentlocale: pt-PT
ms.lasthandoff: 07/02/2020
ms.locfileid: "78302190"
---
# <a name="replicate-azure-cosmos-db"></a>REPLICA (Azure Cosmos DB)
 Repete um valor de cadeia um número especificado de vezes.
  
## <a name="syntax"></a>Sintaxe
  
```sql
REPLICATE(<str_expr>, <num_expr>)
```  
  
## <a name="arguments"></a>Argumentos
  
*str_expr*  
   É uma expressão de corda.
  
*num_expr*  
   É uma expressão numérica. Se *num_expr* for negativo ou não finito, o resultado é indefinido.
  
## <a name="return-types"></a>Tipos de retorno
  
  Devolve uma expressão de corda.
  
## <a name="remarks"></a>Observações
  O comprimento máximo do resultado é de 10.000 caracteres, ou seja, (comprimento*str_expr** *num_expr*) <= 10.000.

## <a name="examples"></a>Exemplos
  
  O exemplo a seguir mostra como se usa `REPLICATE` numa consulta.
  
```sql
SELECT REPLICATE("a", 3) AS replicate
```  
  
 Aqui está o conjunto de resultados.
  
```json
[{"replicate": "aaa"}]
```  

## <a name="remarks"></a>Observações

Esta função do sistema não utilizará o índice.

## <a name="next-steps"></a>Próximos passos

- [Funções de corda Azure Cosmos DB](sql-query-string-functions.md)
- [Funciona O sistema Azure Cosmos DB](sql-query-system-functions.md)
- [Introdução ao Azure Cosmos DB](introduction.md)
