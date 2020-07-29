---
title: COS em Azure Cosmos DB linguagem de consulta
description: Saiba como funciona o sistema Cosine (COS) SQL em Azure Cosmos DB devolve a cosina trigonométrica do ângulo especificado, em radiais, na expressão especificada
author: ginamr
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 03/03/2020
ms.author: girobins
ms.custom: query-reference
ms.openlocfilehash: 40d277ff38691e2cb74bd4d5d78a666c304acfcd
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 07/02/2020
ms.locfileid: "78304026"
---
# <a name="cos-azure-cosmos-db"></a>COS (Azure Cosmos DB)
 Devolve o cosine trigonométrico do ângulo especificado, em radiais, na expressão especificada.  
  
## <a name="syntax"></a>Sintaxe
  
```sql
COS(<numeric_expr>)  
```  
  
## <a name="arguments"></a>Argumentos
  
*numeric_expr*  
   É uma expressão numérica.  
  
## <a name="return-types"></a>Tipos de retorno
  
  Devolve uma expressão numérica.  
  
## <a name="examples"></a>Exemplos
  
  O exemplo a seguir calcula `COS` o ângulo especificado.  
  
```sql
SELECT COS(14.78) AS cos  
```  
  
 Aqui está o conjunto de resultados.  
  
```json
[{"cos": -0.59946542619465426}]  
```  

## <a name="remarks"></a>Observações

Esta função do sistema não utilizará o índice.

## <a name="next-steps"></a>Próximos passos

- [Funções matemáticas Azure Cosmos DB](sql-query-mathematical-functions.md)
- [Funciona O sistema Azure Cosmos DB](sql-query-system-functions.md)
- [Introdução ao Azure Cosmos DB](introduction.md)
