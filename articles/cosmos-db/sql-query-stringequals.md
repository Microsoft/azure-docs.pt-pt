---
title: StringEquals em Azure Cosmos DB linguagem de consulta
description: Saiba como o sistema StringEquals SQL funciona no Azure Cosmos DB retorna um Boolean indicando se a primeira expressão de corda corresponde à segunda
author: timsander1
ms.service: cosmos-db
ms.subservice: cosmosdb-sql
ms.topic: conceptual
ms.date: 05/20/2020
ms.author: tisande
ms.custom: query-reference
ms.openlocfilehash: 47d899534de535b5cd6a7c3fb2df78cdadbe11f2
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/29/2021
ms.locfileid: "93338055"
---
# <a name="stringequals-azure-cosmos-db"></a>STRINGEQUALS (Azure Cosmos DB)
[!INCLUDE[appliesto-sql-api](includes/appliesto-sql-api.md)]

 Devolve um Boolean indicando se a primeira expressão de corda corresponde à segunda.  
  
## <a name="syntax"></a>Sintaxe
  
```sql
STRINGEQUALS(<str_expr1>, <str_expr2> [, <bool_expr>])  
```  
  
## <a name="arguments"></a>Argumentos
  
*str_expr1*  
   É a primeira expressão de corda a comparar.  
  
*str_expr2*  
   É a segunda expressão de corda para comparar.  

*bool_expr* Valor opcional para ignorar o caso. Quando definido como verdadeiro, StringEquals fará uma pesquisa insensível a caso. Quando não especificado, este valor é falso.
  
## <a name="return-types"></a>Tipos de retorno
  
  Devolve uma expressão booleana.  
  
## <a name="examples"></a>Exemplos
  
  O exemplo a seguir verifica se "abc" corresponde a "abc" e se "abc" corresponde a "ABC".  
  
```sql
SELECT STRINGEQUALS("abc", "abc", false) AS c1, STRINGEQUALS("abc", "ABC", false) AS c2,  STRINGEQUALS("abc", "ABC", true) AS c3
```  
  
 Aqui está o conjunto de resultados.  
  
```json
[
    {
        "c1": true,
        "c2": false,
        "c3": true
    }
]
```  

## <a name="remarks"></a>Observações

Esta função do sistema beneficiará de um [índice de intervalo.](index-policy.md#includeexclude-strategy)

## <a name="next-steps"></a>Passos seguintes

- [Funções de corda Azure Cosmos DB](sql-query-string-functions.md)
- [Funciona O sistema Azure Cosmos DB](sql-query-system-functions.md)
- [Introdução ao Azure Cosmos DB](introduction.md)
