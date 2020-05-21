---
title: CONTÉM em linguagem de consulta de Azure Cosmos DB
description: Saiba como o sistema CONTAINS SQL funciona em Azure Cosmos DB devolve uma Boolean indicando se a primeira expressão de corda contém a segunda
author: ginamr
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 05/20/2020
ms.author: girobins
ms.custom: query-reference
ms.openlocfilehash: 0de34e6e0e238887b8f75ae2397e9e650eaac340
ms.sourcegitcommit: 958f086136f10903c44c92463845b9f3a6a5275f
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 05/20/2020
ms.locfileid: "83711708"
---
# <a name="contains-azure-cosmos-db"></a>CONTÉM (Azure Cosmos DB)

 Devolve uma Boolean indicando se a primeira expressão de corda contém a segunda.  
  
## <a name="syntax"></a>Sintaxe
  
```sql
CONTAINS(<str_expr1>, <str_expr2> [, <bool_expr>])  
```  
  
## <a name="arguments"></a>Argumentos
  
*str_expr1*  
   É a expressão de corda a ser revistada.  
  
*str_expr2*  
   É a expressão de corda para encontrar.  

*bool_expr* Valor opcional para ignorar caso. Quando for em vigor, a CONTAINS fará uma pesquisa insensível em casos. Quando não especificado, este valor é falso.
  
## <a name="return-types"></a>Tipos de retorno
  
  Devolve uma expressão booleana.  
  
## <a name="examples"></a>Exemplos
  
  Verifica-se se "abc" contém "ab" e se "abc" contém "d".  
  
```sql
SELECT CONTAINS("abc", "ab") AS c1, CONTAINS("abc", "d") AS c2
```  
  
 Aqui está o conjunto de resultados.  
  
```json
[{"c1": true, "c2": false}]  
```  

## <a name="remarks"></a>Observações

Esta função do sistema beneficiará de um índice de [alcance](index-policy.md#includeexclude-strategy).

## <a name="next-steps"></a>Próximos passos

- [Funções de corda Azure Cosmos DB](sql-query-string-functions.md)
- [Funcionamento do sistema Azure Cosmos DB](sql-query-system-functions.md)
- [Introdução ao Azure Cosmos DB](introduction.md)
