---
title: COT em linguagem de consulta de Azure Cosmos DB
description: Saiba como funciona o sistema SQL Cotangent(COT) em Azure Cosmos DB devolve a cotangente trigonométrica do ângulo especificado, em radianos, na expressão numérica especificada
author: ginamr
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 03/03/2020
ms.author: girobins
ms.custom: query-reference
ms.openlocfilehash: 25c907644f58ee40ea08e5636d68dc0e84564a28
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/28/2020
ms.locfileid: "78299492"
---
# <a name="cot-azure-cosmos-db"></a>BERÇO (Azure Cosmos DB)
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
  
  O exemplo seguinte `COT` calcula o ângulo especificado.  
  
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
- [Funcionamento do sistema Azure Cosmos DB](sql-query-system-functions.md)
- [Introdução ao Azure Cosmos DB](introduction.md)
