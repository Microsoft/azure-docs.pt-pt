---
title: SIN na linguagem de consulta Azure Cosmos DB
description: Saiba mais sobre o SIN da função do sistema SQL no Azure Cosmos DB.
author: ginamr
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 09/13/2019
ms.author: girobins
ms.custom: query-reference
ms.openlocfilehash: b4450ea9dd50f8093fa9569d7f82fe124022c3a1
ms.sourcegitcommit: 7f6d986a60eff2c170172bd8bcb834302bb41f71
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 09/27/2019
ms.locfileid: "71349462"
---
# <a name="sin-azure-cosmos-db"></a>SIN (Azure Cosmos DB)
 Devolve o seno trigonométricos do ângulo especificado, em radianos, a expressão especificada.  
  
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
  
  O exemplo a seguir calcula o `SIN` do ângulo especificado.  
  
```sql
SELECT SIN(45.175643) AS sin  
```  
  
 Aqui está o conjunto de resultados.  
  
```json
[{"sin": 0.929607286611012}]  
```  

## <a name="next-steps"></a>Passos seguintes

- [Funções matemáticas Azure Cosmos DB](sql-query-mathematical-functions.md)
- [Funções do sistema Azure Cosmos DB](sql-query-system-functions.md)
- [Introdução ao Azure Cosmos DB](introduction.md)
