---
title: LTRIM em linguagem de consulta de Azure Cosmos DB
description: Saiba mais sobre a função do sistema LTRIM SQL em Azure Cosmos DB para devolver uma expressão de cadeia depois de remover os espaços em branco principais
author: ginamr
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 09/13/2019
ms.author: girobins
ms.custom: query-reference
ms.openlocfilehash: 08c069de70684a8562e86963ddb2e84ee889e7cc
ms.sourcegitcommit: f915d8b43a3cefe532062ca7d7dbbf569d2583d8
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/05/2020
ms.locfileid: "78302258"
---
# <a name="ltrim-azure-cosmos-db"></a>LTRIM (Azure Cosmos DB)
 Devolve uma expressão de cadeia de caracteres depois que ele remove espaços em branco à esquerda.  
  
## <a name="syntax"></a>Sintaxe
  
```sql
LTRIM(<str_expr>)  
```  
  
## <a name="arguments"></a>Argumentos
  
*str_expr*  
   É uma expressão de cordas.  
  
## <a name="return-types"></a>Tipos de retorno
  
  Devolve uma expressão de cadeia.  
  
## <a name="examples"></a>Exemplos
  
  O exemplo que se segue mostra como usá`LTRIM` dentro de uma consulta.  
  
```sql
SELECT LTRIM("  abc") AS l1, LTRIM("abc") AS l2, LTRIM("abc   ") AS l3 
```  
  
 Aqui está o conjunto de resultados.  
  
```json
[{"l1": "abc", "l2": "abc", "l3": "abc   "}]  
```  

## <a name="remarks"></a>Observações

Esta função do sistema não utilizará o índice.

## <a name="next-steps"></a>Passos seguintes

- [Funções de corda Azure Cosmos DB](sql-query-string-functions.md)
- [Funcionamento do sistema Azure Cosmos DB](sql-query-system-functions.md)
- [Introdução ao Azure Cosmos DB](introduction.md)
