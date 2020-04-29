---
title: TETO na linguagem de consulta de Azure Cosmos DB
description: Saiba como funciona o sistema CEILING SQL em Azure Cosmos DB devolve o menor valor inteiro maior do que, ou igual, à expressão numérica especificada.
author: ginamr
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 09/13/2019
ms.author: girobins
ms.custom: query-reference
ms.openlocfilehash: 81f113aa51a7f739b506ec7e3eb5bf2cb9f49a03
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/28/2020
ms.locfileid: "78302649"
---
# <a name="ceiling-azure-cosmos-db"></a>TETO (Azure Cosmos DB)
 Devolve o menor valor inteiro maior ou igual à expressão numérica especificada.  
  
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
  
  O exemplo seguinte mostra valores numéricos, `CEILING` negativos e nulos positivos com a função.  
  
```sql
SELECT CEILING(123.45) AS c1, CEILING(-123.45) AS c2, CEILING(0.0) AS c3  
```  
  
 Aqui está o conjunto de resultados.  
  
```json
[{c1: 124, c2: -123, c3: 0}]  
```  

## <a name="remarks"></a>Observações

Esta função do sistema beneficiará de um índice de [alcance](index-policy.md#includeexclude-strategy).

## <a name="next-steps"></a>Passos seguintes

- [Funções matemáticas Azure Cosmos DB](sql-query-mathematical-functions.md)
- [Funcionamento do sistema Azure Cosmos DB](sql-query-system-functions.md)
- [Introdução ao Azure Cosmos DB](introduction.md)
