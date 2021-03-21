---
title: ATAN em Azure Cosmos DB linguagem de consulta
description: Saiba como o sistema Arctangent (ATAN) SQL funciona em Azure Cosmos DB devolve o ângulo, em radianos, cuja tangente é a expressão numérica especificada
author: ginamr
ms.service: cosmos-db
ms.subservice: cosmosdb-sql
ms.topic: conceptual
ms.date: 03/04/2020
ms.author: girobins
ms.custom: query-reference
ms.openlocfilehash: 50c678ad3f382ebdcf23c0ba125a47d7aaa7d7ec
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/19/2021
ms.locfileid: "93332445"
---
# <a name="atan-azure-cosmos-db"></a>ATAN (Azure Cosmos DB)
[!INCLUDE[appliesto-sql-api](includes/appliesto-sql-api.md)]

 Devolve o ângulo, em radians, cuja tangente é a expressão numérica especificada. Isto também é chamado arctangent.  
  
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
  
  O exemplo a seguir devolve `ATAN` o valor especificado.  
  
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
- [Funciona O sistema Azure Cosmos DB](sql-query-system-functions.md)
- [Introdução ao Azure Cosmos DB](introduction.md)
