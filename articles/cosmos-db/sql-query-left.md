---
title: ESQUERDA em Azure Cosmos DB linguagem de consulta
description: Saiba mais sobre a função do sistema SQL LEFT em Azure Cosmos DB.
author: ginamr
ms.service: cosmos-db
ms.subservice: cosmosdb-sql
ms.topic: conceptual
ms.date: 09/13/2019
ms.author: girobins
ms.custom: query-reference
ms.openlocfilehash: 647bee87283f26b0d1ce832b2b2aaac690c05b92
ms.sourcegitcommit: fa90cd55e341c8201e3789df4cd8bd6fe7c809a3
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 11/04/2020
ms.locfileid: "93333737"
---
# <a name="left-azure-cosmos-db"></a>ESQUERDA (Azure Cosmos DB)
[!INCLUDE[appliesto-sql-api](includes/appliesto-sql-api.md)]

 Devolve a parte esquerda de uma corda com o número especificado de caracteres.  
  
## <a name="syntax"></a>Sintaxe
  
```sql
LEFT(<str_expr>, <num_expr>)  
```  
  
## <a name="arguments"></a>Argumentos
  
*str_expr*  
   É a expressão de corda para extrair caracteres de.  
  
*num_expr*  
   É uma expressão numérica que especifica o número de caracteres.  
  
## <a name="return-types"></a>Tipos de retorno
  
  Devolve uma expressão de corda.  
  
## <a name="examples"></a>Exemplos
  
  O exemplo a seguir devolve a parte esquerda do "abc" para vários valores de comprimento.  
  
```sql
SELECT LEFT("abc", 1) AS l1, LEFT("abc", 2) AS l2 
```  
  
 Aqui está o conjunto de resultados.  
  
```json
[{"l1": "a", "l2": "ab"}]  
```  

## <a name="remarks"></a>Observações

Esta função do sistema beneficiará de um [índice de intervalo.](index-policy.md#includeexclude-strategy)

## <a name="next-steps"></a>Passos seguintes

- [Funções de corda Azure Cosmos DB](sql-query-string-functions.md)
- [Funciona O sistema Azure Cosmos DB](sql-query-system-functions.md)
- [Introdução ao Azure Cosmos DB](introduction.md)
