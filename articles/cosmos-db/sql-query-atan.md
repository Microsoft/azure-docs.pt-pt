---
title: ATAN em linguagem de consulta de Azure Cosmos DB
description: Saiba como o sistema Arctangent (ATAN ) SQL funciona em Azure Cosmos DB devolve o ângulo, em radianos, cuja tangente é a expressão numérica especificada
author: ginamr
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 03/04/2020
ms.author: girobins
ms.custom: query-reference
ms.openlocfilehash: 899c94a939be7825dca82522eab235bde9252896
ms.sourcegitcommit: f915d8b43a3cefe532062ca7d7dbbf569d2583d8
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/05/2020
ms.locfileid: "78302683"
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
  
  O exemplo seguinte devolve o `ATAN` do valor especificado.  
  
```sql
SELECT ATAN(-45.01) AS atan  
```  
  
 Aqui está o conjunto de resultados.  
  
```json
[{"atan": -1.5485826962062663}]  
```  
  
## <a name="remarks"></a>Observações

Esta função do sistema não utilizará o índice.

## <a name="next-steps"></a>Passos seguintes

- [Funções matemáticas Azure Cosmos DB](sql-query-mathematical-functions.md)
- [Funcionamento do sistema Azure Cosmos DB](sql-query-system-functions.md)
- [Introdução ao Azure Cosmos DB](introduction.md)
