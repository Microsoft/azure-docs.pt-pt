---
title: COS na linguagem de consulta Azure Cosmos DB
description: Saiba mais sobre como a função de sistema do cosseno (COS) do SQL no Azure Cosmos DB retorna o cosseno trigonométrica do ângulo especificado, em radianos, na expressão especificada
author: ginamr
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 09/13/2019
ms.author: girobins
ms.custom: query-reference
ms.openlocfilehash: 05546ef531059e05a1426a288a48446d63d8e5df
ms.sourcegitcommit: 9405aad7e39efbd8fef6d0a3c8988c6bf8de94eb
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 12/05/2019
ms.locfileid: "74873408"
---
# <a name="cos-azure-cosmos-db"></a>COS (Azure Cosmos DB)
 Devolve o cosseno trigonométricos do ângulo especificado, em radianos, a expressão especificada.  
  
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
  
  O exemplo a seguir calcula a `COS` do ângulo especificado.  
  
```sql
SELECT COS(14.78) AS cos  
```  
  
 Aqui está o conjunto de resultados.  
  
```json
[{"cos": -0.59946542619465426}]  
```  

## <a name="next-steps"></a>Passos seguintes

- [Funções matemáticas Azure Cosmos DB](sql-query-mathematical-functions.md)
- [Funções do sistema Azure Cosmos DB](sql-query-system-functions.md)
- [Introdução ao Azure Cosmos DB](introduction.md)
