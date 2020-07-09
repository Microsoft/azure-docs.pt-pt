---
title: ASIN em Azure Cosmos DB linguagem de consulta
description: Saiba como o sistema SQL Arcsine (ASIN) funciona em Azure Cosmos DB devolve o ângulo, em radians, cujo sine é a expressão numérica especificada
author: ginamr
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 03/04/2020
ms.author: girobins
ms.custom: query-reference
ms.openlocfilehash: 8b70738a439b6c64a84a63adf63c83995530e92e
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.contentlocale: pt-PT
ms.lasthandoff: 07/02/2020
ms.locfileid: "78302700"
---
# <a name="asin-azure-cosmos-db"></a>ASIN (Azure Cosmos DB)
 Devolve o ângulo, em radians, cujo seno é a expressão numérica especificada. Isto também é chamado de arcsine.  
  
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
  
  O exemplo a seguir devolve o `ASIN` de -1.  
  
```sql
SELECT ASIN(-1) AS asin  
```  
  
 Aqui está o conjunto de resultados.  
  
```json
[{"asin": -1.5707963267948966}]  
```  

## <a name="remarks"></a>Observações

Esta função do sistema não utilizará o índice.

## <a name="next-steps"></a>Próximos passos

- [Funções matemáticas Azure Cosmos DB](sql-query-mathematical-functions.md)
- [Funciona O sistema Azure Cosmos DB](sql-query-system-functions.md)
- [Introdução ao Azure Cosmos DB](introduction.md)
