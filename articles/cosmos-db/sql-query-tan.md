---
title: TAN em Azure Cosmos DB linguagem de consulta
description: Saiba mais sobre a função do sistema SQL TAN em Azure Cosmos DB.
author: ginamr
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 03/04/2020
ms.author: girobins
ms.custom: query-reference
ms.openlocfilehash: 9d7187ba116067445e835769fc33aa70677ef80b
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/09/2020
ms.locfileid: "78301986"
---
# <a name="tan-azure-cosmos-db"></a>TAN (Azure Cosmos DB)
 Devolve a tangente do ângulo especificado, em radiais, na expressão especificada.  
  
## <a name="syntax"></a>Sintaxe
  
```sql
TAN (<numeric_expr>)  
```  
  
## <a name="arguments"></a>Argumentos
  
*numeric_expr*  
   É uma expressão numérica.  
  
## <a name="return-types"></a>Tipos de retorno
  
  Devolve uma expressão numérica.  
  
## <a name="examples"></a>Exemplos
  
  O exemplo a seguir calcula a tangente de PI()/2.  
  
```sql
SELECT TAN(PI()/2) AS tan 
```  
  
 Aqui está o conjunto de resultados.  
  
```json
[{"tan": 16331239353195370 }]  
```  

## <a name="remarks"></a>Observações

Esta função do sistema não utilizará o índice.

## <a name="next-steps"></a>Passos seguintes

- [Funções matemáticas Azure Cosmos DB](sql-query-mathematical-functions.md)
- [Funciona O sistema Azure Cosmos DB](sql-query-system-functions.md)
- [Introdução ao Azure Cosmos DB](introduction.md)
