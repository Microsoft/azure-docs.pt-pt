---
title: SUBSTRING na linguagem de consulta do Azure Cosmos DB
description: Saiba mais sobre a função do sistema SQL SUBSTRING em Azure Cosmos DB.
author: ginamr
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 09/13/2019
ms.author: girobins
ms.custom: query-reference
ms.openlocfilehash: d4462fc407093b23510bddfae4d9f55d68f8c0fa
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/28/2020
ms.locfileid: "78303703"
---
# <a name="substring-azure-cosmos-db"></a>SUBSTRING (Azure Cosmos DB)
 Devolve parte de uma expressão de corda a partir da posição de base zero do caracteres especificado e continua até ao comprimento especificado, ou até ao fim da corda.  
  
## <a name="syntax"></a>Sintaxe
  
```sql
SUBSTRING(<str_expr>, <num_expr1>, <num_expr2>)  
```  
  
## <a name="arguments"></a>Argumentos
  
*str_expr*  
   É uma expressão de cordas.
  
*num_expr1*  
   É uma expressão numérica para denotar o personagem inicial. Um valor de 0 é o primeiro personagem de *str_expr.*
  
*num_expr2*  
   É uma expressão numérica para denotar o número máximo de caracteres de *str_expr* a ser devolvido. Um valor de 0 ou menos resulta em corda vazia.

## <a name="return-types"></a>Tipos de retorno
  
  Devolve uma expressão de corda.  
  
## <a name="examples"></a>Exemplos
  
  O exemplo seguinte devolve a subcadeia de "abc" a partir de 1 e por um comprimento de 1 caracteres.  
  
```sql
SELECT SUBSTRING("abc", 1, 1) AS substring  
```  
  
 Aqui está o conjunto de resultados.  
  
```json
[{"substring": "b"}]  
```

## <a name="remarks"></a>Observações

Esta função do sistema beneficiará de um `0`índice de [gama](index-policy.md#includeexclude-strategy) se a posição inicial for .

## <a name="next-steps"></a>Passos seguintes

- [Funções de corda Azure Cosmos DB](sql-query-string-functions.md)
- [Funcionamento do sistema Azure Cosmos DB](sql-query-system-functions.md)
- [Introdução ao Azure Cosmos DB](introduction.md)
