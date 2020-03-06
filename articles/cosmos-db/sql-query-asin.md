---
title: ASIN em língua de consulta de Azure Cosmos DB
description: Saiba como o sistema SQL (Arcsine (ASIN) em Azure Cosmos DB devolve o ângulo, em radianos, cujo seno é a expressão numérica especificada
author: ginamr
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 03/04/2020
ms.author: girobins
ms.custom: query-reference
ms.openlocfilehash: 8b70738a439b6c64a84a63adf63c83995530e92e
ms.sourcegitcommit: f915d8b43a3cefe532062ca7d7dbbf569d2583d8
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/05/2020
ms.locfileid: "78302700"
---
# <a name="asin-azure-cosmos-db"></a>ASIN (Azure Cosmos DB)
 Devolve o ângulo em radianos, cujo seno é a expressão numérica especificada. Isso também é chamado o arco de seno.  
  
## <a name="syntax"></a>Sintaxe
  
```sql
ASIN(<numeric_expr>)  
```  
  
## <a name="arguments"></a>Argumentos
  
*numeric_expr*  
   É uma expressão numérica.  
  
## <a name="return-types"></a>Tipos de retorno
  
  Devolve uma expressão numérica.  
  
## <a name="examples"></a>Exemplos
  
  O exemplo seguinte devolve o `ASIN` de -1.  
  
```sql
SELECT ASIN(-1) AS asin  
```  
  
 Aqui está o conjunto de resultados.  
  
```json
[{"asin": -1.5707963267948966}]  
```  

## <a name="remarks"></a>Observações

Esta função do sistema não utilizará o índice.

## <a name="next-steps"></a>Passos seguintes

- [Funções matemáticas Azure Cosmos DB](sql-query-mathematical-functions.md)
- [Funcionamento do sistema Azure Cosmos DB](sql-query-system-functions.md)
- [Introdução ao Azure Cosmos DB](introduction.md)
