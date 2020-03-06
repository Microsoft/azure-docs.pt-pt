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
ms.sourcegitcommit: f915d8b43a3cefe532062ca7d7dbbf569d2583d8
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/05/2020
ms.locfileid: "78303703"
---
# <a name="substring-azure-cosmos-db"></a>SUBSTRING (Azure Cosmos DB)
 Devolve a parte de uma expressão de cadeia de caracteres a partir da posição caractere especificado baseado em zero e continua ao comprimento especificado, ou ao fim da cadeia de caracteres.  
  
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
  
  Devolve uma expressão de cadeia.  
  
## <a name="examples"></a>Exemplos
  
  O exemplo seguinte devolve a subcadeia de "abc" começando em 1 e durante um período de 1 caráter.  
  
```sql
SELECT SUBSTRING("abc", 1, 1) AS substring  
```  
  
 Aqui está o conjunto de resultados.  
  
```json
[{"substring": "b"}]  
```

## <a name="remarks"></a>Observações

Esta função do sistema beneficiará de um índice de [gama](index-policy.md#includeexclude-strategy) se a posição inicial for `0`.

## <a name="next-steps"></a>Passos seguintes

- [Funções de corda Azure Cosmos DB](sql-query-string-functions.md)
- [Funcionamento do sistema Azure Cosmos DB](sql-query-system-functions.md)
- [Introdução ao Azure Cosmos DB](introduction.md)
