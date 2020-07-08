---
title: SUBSTRING em Azure Cosmos DB linguagem de consulta
description: Saiba mais sobre a função do sistema SQL SUBstring em Azure Cosmos DB.
author: ginamr
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 09/13/2019
ms.author: girobins
ms.custom: query-reference
ms.openlocfilehash: d4462fc407093b23510bddfae4d9f55d68f8c0fa
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.contentlocale: pt-PT
ms.lasthandoff: 07/02/2020
ms.locfileid: "78303703"
---
# <a name="substring-azure-cosmos-db"></a>SUBSTRING (Azure Cosmos DB)
 Devolve parte de uma expressão de corda a partir da posição de base de caracteres especificado e continua até ao comprimento especificado, ou até à extremidade da cadeia.  
  
## <a name="syntax"></a>Sintaxe
  
```sql
SUBSTRING(<str_expr>, <num_expr1>, <num_expr2>)  
```  
  
## <a name="arguments"></a>Argumentos
  
*str_expr*  
   É uma expressão de corda.
  
*num_expr1*  
   É uma expressão numérica para denotar o personagem inicial. Um valor de 0 é o primeiro personagem de *str_expr*.
  
*num_expr2*  
   É uma expressão numérica para denotar o número máximo de caracteres de *str_expr* a ser devolvido. Um valor de 0 ou menos resulta em corda vazia.

## <a name="return-types"></a>Tipos de retorno
  
  Devolve uma expressão de corda.  
  
## <a name="examples"></a>Exemplos
  
  O exemplo a seguir devolve o sublamamento de "abc" a partir de 1 e para um comprimento de 1 caracteres.  
  
```sql
SELECT SUBSTRING("abc", 1, 1) AS substring  
```  
  
 Aqui está o conjunto de resultados.  
  
```json
[{"substring": "b"}]  
```

## <a name="remarks"></a>Observações

Esta função do sistema beneficiará de um índice de [intervalo](index-policy.md#includeexclude-strategy) se a posição inicial for `0` .

## <a name="next-steps"></a>Próximos passos

- [Funções de corda Azure Cosmos DB](sql-query-string-functions.md)
- [Funciona O sistema Azure Cosmos DB](sql-query-system-functions.md)
- [Introdução ao Azure Cosmos DB](introduction.md)
