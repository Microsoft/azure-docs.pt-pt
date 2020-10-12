---
title: SUBSTITUIR em Azure Cosmos DB linguagem de consulta
description: Saiba mais sobre a função do sistema SQL SUBSTITU em Azure Cosmos DB.
author: ginamr
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 09/13/2019
ms.author: girobins
ms.custom: query-reference
ms.openlocfilehash: 758ac13530752df481d27e7e253f025f5c8d6430
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/09/2020
ms.locfileid: "78302207"
---
# <a name="replace-azure-cosmos-db"></a>SUBSTITUIR (Azure Cosmos DB)
 Substitui todas as ocorrências de um valor de corda especificado por outro valor de cadeia.  
  
## <a name="syntax"></a>Sintaxe
  
```sql
REPLACE(<str_expr1>, <str_expr2>, <str_expr3>)  
```  
  
## <a name="arguments"></a>Argumentos
  
*str_expr1*  
   É a expressão de corda a ser revistada.  
  
*str_expr2*  
   É a expressão de corda para ser encontrada.  
  
*str_expr3*  
   É a expressão de corda para substituir as ocorrências de *str_expr2* em *str_expr1*.  
  
## <a name="return-types"></a>Tipos de retorno
  
  Devolve uma expressão de corda.  
  
## <a name="examples"></a>Exemplos
  
  O exemplo a seguir mostra como se usa `REPLACE` numa consulta.  
  
```sql
SELECT REPLACE("This is a Test", "Test", "desk") AS replace
```  
  
 Aqui está o conjunto de resultados.  
  
```json
[{"replace": "This is a desk"}]  
```  

## <a name="remarks"></a>Observações

Esta função do sistema não utilizará o índice.

## <a name="next-steps"></a>Passos seguintes

- [Funções de corda Azure Cosmos DB](sql-query-string-functions.md)
- [Funciona O sistema Azure Cosmos DB](sql-query-system-functions.md)
- [Introdução ao Azure Cosmos DB](introduction.md)
