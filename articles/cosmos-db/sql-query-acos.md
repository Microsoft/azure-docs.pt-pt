---
title: ACOS em linguagem de consulta do Azure Cosmos DB
description: Saiba como o sistema SQL ACOS (arccosice) em Azure Cosmos DB devolve o ângulo, em radianos, cujo cosino é a expressão numérica especificada
author: ginamr
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 03/03/2020
ms.author: girobins
ms.custom: query-reference
ms.openlocfilehash: 733d6b009f03d61c37170cc506a3b2ec842d7c47
ms.sourcegitcommit: f915d8b43a3cefe532062ca7d7dbbf569d2583d8
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/05/2020
ms.locfileid: "78300966"
---
# <a name="acos-azure-cosmos-db"></a>ACOS (Azure Cosmos DB)
 Devolve o ângulo em radianos, cujo co-seno é a expressão numérica especificada; Também chamado de arco de cosseno.  
  
## <a name="syntax"></a>Sintaxe
  
```sql
ACOS(<numeric_expr>)  
```  
  
## <a name="arguments"></a>Argumentos
  
*numeric_expr*  
   É uma expressão numérica.  
  
## <a name="return-types"></a>Tipos de retorno
  
  Devolve uma expressão numérica.  
  
## <a name="examples"></a>Exemplos
  
  O exemplo seguinte devolve o `ACOS` de -1.  
  
```sql
SELECT ACOS(-1) AS acos 
```  
  
 Aqui está o conjunto de resultados.  
  
```json
[{"acos": 3.1415926535897931}]  
```  

## <a name="remarks"></a>Observações

Esta função do sistema não utilizará o índice.

## <a name="next-steps"></a>Passos seguintes

- [Funções matemáticas Azure Cosmos DB](sql-query-mathematical-functions.md)
- [Funcionamento do sistema Azure Cosmos DB](sql-query-system-functions.md)
- [Introdução ao Azure Cosmos DB](introduction.md)
