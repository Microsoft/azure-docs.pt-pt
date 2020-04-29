---
title: CONCAT em linguagem de consulta do Azure Cosmos DB
description: Saiba como funciona o sistema CONCAT SQL em Azure Cosmos DB devolve uma corda que é o resultado da concatenação de dois ou mais valores de cadeia
author: ginamr
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 03/03/2020
ms.author: girobins
ms.custom: query-reference
ms.openlocfilehash: c8a0941376ed74d7f8cb819d78df43eb9f0b7bd1
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/28/2020
ms.locfileid: "78302615"
---
# <a name="concat-azure-cosmos-db"></a>CONCAT (Azure Cosmos DB)
 Devolve uma corda que é o resultado da concatenação de dois ou mais valores de cadeia.  
  
## <a name="syntax"></a>Sintaxe
  
```sql
CONCAT(<str_expr1>, <str_expr2> [, <str_exprN>])  
```  
  
## <a name="arguments"></a>Argumentos
  
*str_expr*  
   É uma expressão de corda para concatenar aos outros valores. A `CONCAT` função requer pelo menos dois *argumentos str_expr.*  
  
## <a name="return-types"></a>Tipos de retorno
  
  Devolve uma expressão de corda.  
  
## <a name="examples"></a>Exemplos
  
  O exemplo seguinte devolve a cadeia concatenada dos valores especificados.  
  
```sql
SELECT CONCAT("abc", "def") AS concat  
```  
  
 Aqui está o conjunto de resultados.  
  
```json
[{"concat": "abcdef"}]  
```  
  
## <a name="remarks"></a>Observações

Esta função do sistema não utilizará o índice.

## <a name="next-steps"></a>Passos seguintes

- [Funções de corda Azure Cosmos DB](sql-query-string-functions.md)
- [Funcionamento do sistema Azure Cosmos DB](sql-query-system-functions.md)
- [Introdução ao Azure Cosmos DB](introduction.md)
