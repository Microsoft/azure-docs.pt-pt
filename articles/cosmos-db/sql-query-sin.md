---
title: SIN em Azure Cosmos DB linguagem de consulta
description: Saiba mais sobre a função do sistema SQL SIN em Azure Cosmos DB.
author: ginamr
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 03/03/2020
ms.author: girobins
ms.custom: query-reference
ms.openlocfilehash: 25e7cf66fdd55a0b641c35443e38b0a67cbe365d
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 07/02/2020
ms.locfileid: "78303108"
---
# <a name="sin-azure-cosmos-db"></a>SIN (Azure Cosmos DB)
 Devolve o seno trigonométrico do ângulo especificado, em radiais, na expressão especificada.  
  
## <a name="syntax"></a>Sintaxe
  
```sql
SIN(<numeric_expr>)  
```  
  
## <a name="arguments"></a>Argumentos
  
*numeric_expr*  
   É uma expressão numérica.  
  
## <a name="return-types"></a>Tipos de retorno
  
  Devolve uma expressão numérica.  
  
## <a name="examples"></a>Exemplos
  
  O exemplo a seguir calcula `SIN` o ângulo especificado.  
  
```sql
SELECT SIN(45.175643) AS sin  
```  
  
 Aqui está o conjunto de resultados.  
  
```json
[{"sin": 0.929607286611012}]  
```  

## <a name="remarks"></a>Observações

Esta função do sistema não utilizará o índice.

## <a name="next-steps"></a>Próximos passos

- [Funções matemáticas Azure Cosmos DB](sql-query-mathematical-functions.md)
- [Funciona O sistema Azure Cosmos DB](sql-query-system-functions.md)
- [Introdução ao Azure Cosmos DB](introduction.md)
