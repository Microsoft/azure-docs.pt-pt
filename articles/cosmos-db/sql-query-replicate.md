---
title: REPLICAr na linguagem de consulta do Azure Cosmos DB
description: Saiba mais sobre a replicação de função do sistema SQL no Azure Cosmos DB.
author: ginamr
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 09/13/2019
ms.author: girobins
ms.custom: query-reference
ms.openlocfilehash: 5e0b7f29c503daa8a95dcc46238e60728c0cec50
ms.sourcegitcommit: 7f6d986a60eff2c170172bd8bcb834302bb41f71
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 09/27/2019
ms.locfileid: "71349566"
---
# <a name="replicate-azure-cosmos-db"></a>REPLICAte (Azure Cosmos DB)
 Repete-se um valor de cadeia de caracteres um número de vezes especificado.
  
## <a name="syntax"></a>Sintaxe
  
```sql
REPLICATE(<str_expr>, <num_expr>)
```  
  
## <a name="arguments"></a>Argumentos
  
*str_expr*  
   É uma expressão de cadeia de caracteres.
  
*num_expr*  
   É uma expressão numérica. Se *num_expr* for negativo ou não finito, o resultado será indefinido.
  
## <a name="return-types"></a>Tipos de retorno
  
  Devolve uma expressão de cadeia.
  
## <a name="remarks"></a>Observações
  O comprimento máximo do resultado é de 10.000 caracteres, ou seja, (length (*str_expr*) * *num_expr*) < = 10.000.

## <a name="examples"></a>Exemplos
  
  O exemplo a seguir mostra como usar `REPLICATE` em uma consulta.
  
```sql
SELECT REPLICATE("a", 3) AS replicate
```  
  
 Aqui está o conjunto de resultados.
  
```json
[{"replicate": "aaa"}]
```  

## <a name="next-steps"></a>Passos seguintes

- [Funções de cadeia de caracteres Azure Cosmos DB](sql-query-string-functions.md)
- [Funções do sistema Azure Cosmos DB](sql-query-system-functions.md)
- [Introdução ao Azure Cosmos DB](introduction.md)
