---
title: ESQUERDA em linguagem de consulta de Azure Cosmos DB
description: Saiba mais sobre a função do sistema SQL LEFT in Azure Cosmos DB.
author: ginamr
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 09/13/2019
ms.author: girobins
ms.custom: query-reference
ms.openlocfilehash: 0eac35a91e4d5158335d6797d49a09f8f6f391e3
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/28/2020
ms.locfileid: "78303754"
---
# <a name="left-azure-cosmos-db"></a>ESQUERDA (Azure Cosmos DB)
 Devolve a parte esquerda de uma corda com o número especificado de caracteres.  
  
## <a name="syntax"></a>Sintaxe
  
```sql
LEFT(<str_expr>, <num_expr>)  
```  
  
## <a name="arguments"></a>Argumentos
  
*str_expr*  
   É a expressão de corda para extrair personagens de.  
  
*num_expr*  
   É uma expressão numérica que especifica o número de caracteres.  
  
## <a name="return-types"></a>Tipos de retorno
  
  Devolve uma expressão de corda.  
  
## <a name="examples"></a>Exemplos
  
  O exemplo seguinte devolve a parte esquerda do "abc" para vários valores de comprimento.  
  
```sql
SELECT LEFT("abc", 1) AS l1, LEFT("abc", 2) AS l2 
```  
  
 Aqui está o conjunto de resultados.  
  
```json
[{"l1": "a", "l2": "ab"}]  
```  

## <a name="remarks"></a>Observações

Esta função do sistema beneficiará de um índice de [alcance](index-policy.md#includeexclude-strategy).

## <a name="next-steps"></a>Passos seguintes

- [Funções de corda Azure Cosmos DB](sql-query-string-functions.md)
- [Funcionamento do sistema Azure Cosmos DB](sql-query-system-functions.md)
- [Introdução ao Azure Cosmos DB](introduction.md)
