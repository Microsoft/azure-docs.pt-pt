---
title: ABS na linguagem de consulta Azure Cosmos DB
description: Saiba como a função de sistema SQL absoluta (ABS) no Azure Cosmos DB retorna o valor positivo da expressão numérica especificada
author: ginamr
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 12/02/2019
ms.author: girobins
ms.custom: query-reference
ms.openlocfilehash: 70648dcf28d760c3a81c0cb426cd9b2d3ce96fff
ms.sourcegitcommit: 9405aad7e39efbd8fef6d0a3c8988c6bf8de94eb
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 12/05/2019
ms.locfileid: "74871861"
---
# <a name="abs-azure-cosmos-db"></a>ABS (Azure Cosmos DB)
 Devolve o valor absoluto (positivo) da expressão especificada numérico.  
  
## <a name="syntax"></a>Sintaxe
  
```sql
ABS (<numeric_expr>)  
```  
  
## <a name="arguments"></a>Argumentos
  
*numeric_expr*  
   É uma expressão numérica.  
  
## <a name="return-types"></a>Tipos de retorno
  
  Devolve uma expressão numérica.  
  
## <a name="examples"></a>Exemplos
  
  O exemplo a seguir mostra os resultados do uso da função `ABS` em três números diferentes.  
  
```sql
SELECT ABS(-1) AS abs1, ABS(0) AS abs2, ABS(1) AS abs3 
```  
  
 Aqui está o conjunto de resultados.  
  
```json
[{abs1: 1, abs2: 0, abs3: 1}]  
```  
  

## <a name="next-steps"></a>Passos seguintes

- [Funções matemáticas Azure Cosmos DB](sql-query-mathematical-functions.md)
- [Funções do sistema Azure Cosmos DB](sql-query-system-functions.md)
- [Introdução ao Azure Cosmos DB](introduction.md)
