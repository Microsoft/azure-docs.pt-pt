---
title: REPLICAR na linguagem de consulta do Azure Cosmos DB
description: Saiba mais sobre a função do sistema SQL REPLICA in Azure Cosmos DB.
author: ginamr
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 03/03/2020
ms.author: girobins
ms.custom: query-reference
ms.openlocfilehash: 19fcde522c5cb0355e53a5616145f27fada7dad9
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/28/2020
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
   É uma expressão de cordas.
  
*num_expr*  
   É uma expressão numérica. Se *num_expr* for negativo ou não finito, o resultado é indefinido.
  
## <a name="return-types"></a>Tipos de retorno
  
  Devolve uma expressão de corda.
  
## <a name="remarks"></a>Observações
  O comprimento máximo do resultado é de 10.000 caracteres, ou seja, (comprimento(*str_expr*) * *num_expr*) <= 10.000.

## <a name="examples"></a>Exemplos
  
  O exemplo que se `REPLICATE` segue mostra como usar numa consulta.
  
```sql
SELECT REPLICATE("a", 3) AS replicate
```  
  
 Aqui está o conjunto de resultados.
  
```json
[{"replicate": "aaa"}]
```  

## <a name="remarks"></a>Observações

Esta função do sistema não utilizará o índice.

## <a name="next-steps"></a>Passos seguintes

- [Funções de corda Azure Cosmos DB](sql-query-string-functions.md)
- [Funcionamento do sistema Azure Cosmos DB](sql-query-system-functions.md)
- [Introdução ao Azure Cosmos DB](introduction.md)
