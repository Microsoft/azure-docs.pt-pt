---
title: ATAN na linguagem de consulta Azure Cosmos DB
description: Saiba como a função de sistema SQL do arco tangente (ATAN) em Azure Cosmos DB retorna o ângulo, em radianos, cuja tangente é a expressão numérica especificada
author: ginamr
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 09/13/2019
ms.author: girobins
ms.custom: query-reference
ms.openlocfilehash: 8fd398e343a3e617ceb9f49ca8f31ee776a7f073
ms.sourcegitcommit: 9405aad7e39efbd8fef6d0a3c8988c6bf8de94eb
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 12/05/2019
ms.locfileid: "74873459"
---
# <a name="atan-azure-cosmos-db"></a>ATAN (Azure Cosmos DB)
 Devolve o ângulo em radianos, cuja tangente é a expressão numérica especificada. Isso também é chamado o arco de tangente.  
  
## <a name="syntax"></a>Sintaxe
  
```sql
ATAN(<numeric_expr>)  
```  
  
## <a name="arguments"></a>Argumentos
  
*numeric_expr*  
   É uma expressão numérica.  
  
## <a name="return-types"></a>Tipos de retorno
  
  Devolve uma expressão numérica.  
  
## <a name="examples"></a>Exemplos
  
  O exemplo a seguir retorna o `ATAN` do valor especificado.  
  
```sql
SELECT ATAN(-45.01) AS atan  
```  
  
 Aqui está o conjunto de resultados.  
  
```json
[{"atan": -1.5485826962062663}]  
```  
  

## <a name="next-steps"></a>Passos seguintes

- [Funções matemáticas Azure Cosmos DB](sql-query-mathematical-functions.md)
- [Funções do sistema Azure Cosmos DB](sql-query-system-functions.md)
- [Introdução ao Azure Cosmos DB](introduction.md)
