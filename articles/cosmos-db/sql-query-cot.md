---
title: COT na linguagem de consulta Azure Cosmos DB
description: Saiba como a função de sistema SQL cotangente (COT) no Azure Cosmos DB retorna a cotangente trigonométrica do ângulo especificado, em radianos, na expressão numérica especificada
author: ginamr
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 09/13/2019
ms.author: girobins
ms.custom: query-reference
ms.openlocfilehash: f049d1295eef3e6a45abeaafe8c22d376f90abe1
ms.sourcegitcommit: 9405aad7e39efbd8fef6d0a3c8988c6bf8de94eb
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 12/05/2019
ms.locfileid: "74871504"
---
# <a name="cot-azure-cosmos-db"></a>COT (Azure Cosmos DB)
 Devolve a co-tangente trigonométricos do ângulo especificado, em radianos, a expressão numérica especificado.  
  
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
  
  O exemplo a seguir calcula a `COT` do ângulo especificado.  
  
```sql
SELECT COT(124.1332) AS cot  
```  
  
 Aqui está o conjunto de resultados.  
  
```json
[{"cot": -0.040311998371148884}]  
```  
  

## <a name="next-steps"></a>Passos seguintes

- [Funções matemáticas Azure Cosmos DB](sql-query-mathematical-functions.md)
- [Funções do sistema Azure Cosmos DB](sql-query-system-functions.md)
- [Introdução ao Azure Cosmos DB](introduction.md)
