---
title: TETO na linguagem de consulta Azure Cosmos DB
description: Saiba mais sobre como a função de sistema SQL de teto em Azure Cosmos DB retorna o valor principal da tangente de arco de y/x, expressa em radianos
author: ginamr
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 09/13/2019
ms.author: girobins
ms.custom: query-reference
ms.openlocfilehash: 18462b152b698e7c6bc4c6c1e59511b4bacaa611
ms.sourcegitcommit: 9405aad7e39efbd8fef6d0a3c8988c6bf8de94eb
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 12/05/2019
ms.locfileid: "74873442"
---
# <a name="ceiling-azure-cosmos-db"></a>TETO (Azure Cosmos DB)
 Devolve o menor valor de número inteiro maior que ou igual a, a expressão numérica especificada.  
  
## <a name="syntax"></a>Sintaxe
  
```sql
CEILING (<numeric_expr>)  
```  
  
## <a name="arguments"></a>Argumentos
  
*numeric_expr*  
   É uma expressão numérica.  
  
## <a name="return-types"></a>Tipos de retorno
  
  Devolve uma expressão numérica.  
  
## <a name="examples"></a>Exemplos
  
  O exemplo a seguir mostra valores numéricos positivos, negativos e zero com a função `CEILING`.  
  
```sql
SELECT CEILING(123.45) AS c1, CEILING(-123.45) AS c2, CEILING(0.0) AS c3  
```  
  
 Aqui está o conjunto de resultados.  
  
```json
[{c1: 124, c2: -123, c3: 0}]  
```  

## <a name="next-steps"></a>Passos seguintes

- [Funções matemáticas Azure Cosmos DB](sql-query-mathematical-functions.md)
- [Funções do sistema Azure Cosmos DB](sql-query-system-functions.md)
- [Introdução ao Azure Cosmos DB](introduction.md)
