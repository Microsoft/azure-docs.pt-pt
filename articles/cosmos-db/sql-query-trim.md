---
title: TRIM em linguagem de consulta de Azure Cosmos DB
description: Conheça a função do sistema SQL TRIM em Azure Cosmos DB.
author: ginamr
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 03/04/2020
ms.author: girobins
ms.custom: query-reference
ms.openlocfilehash: 073efbd91e637a17693617b16c7787d61e150ad7
ms.sourcegitcommit: f915d8b43a3cefe532062ca7d7dbbf569d2583d8
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/05/2020
ms.locfileid: "78304247"
---
# <a name="trim-azure-cosmos-db"></a>TRIM (Azure Cosmos DB)
 Devolve uma expressão de cadeia de caracteres depois que ele remove e à direita espaços em branco.  
  
## <a name="syntax"></a>Sintaxe
  
```sql
TRIM(<str_expr>)  
```  
  
## <a name="arguments"></a>Argumentos
  
*str_expr*  
   É uma expressão de cordas.  
  
## <a name="return-types"></a>Tipos de retorno
  
  Devolve uma expressão de cadeia.  
  
## <a name="examples"></a>Exemplos
  
  O exemplo que se segue mostra como usá`TRIM` dentro de uma consulta.  
  
```sql
SELECT TRIM("   abc") AS t1, TRIM("   abc   ") AS t2, TRIM("abc   ") AS t3, TRIM("abc") AS t4
```  
  
 Aqui está o conjunto de resultados.  
  
```json
[{"t1": "abc", "t2": "abc", "t3": "abc", "t4": "abc"}]  
``` 

## <a name="remarks"></a>Observações

Esta função do sistema não utilizará o índice.

## <a name="next-steps"></a>Passos seguintes

- [Funções de corda Azure Cosmos DB](sql-query-string-functions.md)
- [Funcionamento do sistema Azure Cosmos DB](sql-query-system-functions.md)
- [Introdução ao Azure Cosmos DB](introduction.md)
