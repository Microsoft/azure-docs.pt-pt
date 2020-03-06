---
title: SUBSTITUem em linguagem de consulta do Azure Cosmos DB
description: Saiba mais sobre a função do sistema SQL REPLACE in Azure Cosmos DB.
author: ginamr
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 09/13/2019
ms.author: girobins
ms.custom: query-reference
ms.openlocfilehash: 758ac13530752df481d27e7e253f025f5c8d6430
ms.sourcegitcommit: f915d8b43a3cefe532062ca7d7dbbf569d2583d8
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/05/2020
ms.locfileid: "78302207"
---
# <a name="replace-azure-cosmos-db"></a>SUBSTITUIÇÃO (Azure Cosmos DB)
 Substitui todas as ocorrências de um valor de cadeia especificada com outro valor de cadeia de caracteres.  
  
## <a name="syntax"></a>Sintaxe
  
```sql
REPLACE(<str_expr1>, <str_expr2>, <str_expr3>)  
```  
  
## <a name="arguments"></a>Argumentos
  
*str_expr1*  
   É a expressão de corda a ser revistada.  
  
*str_expr2*  
   É a expressão de corda a ser encontrada.  
  
*str_expr3*  
   É a expressão da corda para substituir as ocorrências de *str_expr2* em *str_expr1*.  
  
## <a name="return-types"></a>Tipos de retorno
  
  Devolve uma expressão de cadeia.  
  
## <a name="examples"></a>Exemplos
  
  O exemplo que se segue mostra como usar `REPLACE` numa consulta.  
  
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
- [Funcionamento do sistema Azure Cosmos DB](sql-query-system-functions.md)
- [Introdução ao Azure Cosmos DB](introduction.md)
