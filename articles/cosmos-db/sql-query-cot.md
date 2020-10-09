---
title: COT em Azure Cosmos DB linguagem de consulta
description: Saiba como funciona o sistema SQL Cotangent (COT) em Azure Cosmos DB devolve a cotangente trigonométrica do ângulo especificado, em radianos, na expressão numérica especificada
author: ginamr
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 03/03/2020
ms.author: girobins
ms.custom: query-reference
ms.openlocfilehash: 25c907644f58ee40ea08e5636d68dc0e84564a28
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/09/2020
ms.locfileid: "78299492"
---
# <a name="cot-azure-cosmos-db"></a>COT (Azure Cosmos DB)
 Devolve a cotangente trigonométrica do ângulo especificado, em radianos, na expressão numérica especificada.  
  
## <a name="syntax"></a>Sintaxe
  
```sql
COT(<numeric_expr>)  
```  
  
## <a name="arguments"></a>Argumentos
  
*numeric_expr*  
   É uma expressão numérica.  
  
## <a name="return-types"></a>Tipos de retorno
  
  Devolve uma expressão numérica.  
  
## <a name="examples"></a>Exemplos
  
  O exemplo a seguir calcula `COT` o ângulo especificado.  
  
```sql
SELECT COT(124.1332) AS cot  
```  
  
 Aqui está o conjunto de resultados.  
  
```json
[{"cot": -0.040311998371148884}]  
```  

## <a name="remarks"></a>Observações

Esta função do sistema não utilizará o índice.

## <a name="next-steps"></a>Passos seguintes

- [Funções matemáticas Azure Cosmos DB](sql-query-mathematical-functions.md)
- [Funciona O sistema Azure Cosmos DB](sql-query-system-functions.md)
- [Introdução ao Azure Cosmos DB](introduction.md)
