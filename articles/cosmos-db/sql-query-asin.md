---
title: Asen na linguagem de consulta Azure Cosmos DB
description: Saiba mais sobre como a função de sistema do codeseno (Asen) do SQL em Azure Cosmos DB retorna o ângulo, em radianos, cujo seno é a expressão numérica especificada
author: ginamr
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 09/13/2019
ms.author: girobins
ms.custom: query-reference
ms.openlocfilehash: 3e790eb5ae5eb780637b199a1a65dec2dd02d1bc
ms.sourcegitcommit: 9405aad7e39efbd8fef6d0a3c8988c6bf8de94eb
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 12/05/2019
ms.locfileid: "74871743"
---
# <a name="asin-azure-cosmos-db"></a>Asen (Azure Cosmos DB)
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
  
  O exemplo a seguir retorna o `ASIN` de-1.  
  
```sql
SELECT ASIN(-1) AS asin  
```  
  
 Aqui está o conjunto de resultados.  
  
```json
[{"asin": -1.5707963267948966}]  
```  

## <a name="next-steps"></a>Passos seguintes

- [Funções matemáticas Azure Cosmos DB](sql-query-mathematical-functions.md)
- [Funções do sistema Azure Cosmos DB](sql-query-system-functions.md)
- [Introdução ao Azure Cosmos DB](introduction.md)
