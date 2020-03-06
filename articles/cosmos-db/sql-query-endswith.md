---
title: ENDSWITH em linguagem de consulta de Azure Cosmos DB
description: Saiba mais sobre a função do sistema ENDSWITH SQL em Azure Cosmos DB para devolver um Boolean indicando se a primeira expressão de corda termina com a segunda
author: ginamr
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 03/03/2020
ms.author: girobins
ms.custom: query-reference
ms.openlocfilehash: 37c5a8b3c44c5ac46b837e4d851d22f85aeaf39c
ms.sourcegitcommit: f915d8b43a3cefe532062ca7d7dbbf569d2583d8
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/05/2020
ms.locfileid: "78299453"
---
# <a name="endswith-azure-cosmos-db"></a>ENDSWITH (Azure Cosmos DB)
 Retorna um Booleano indicando se a primeira expressão de cadeia termina com a segunda.  
  
## <a name="syntax"></a>Sintaxe
  
```sql
ENDSWITH(<str_expr1>, <str_expr2>)  
```  
  
## <a name="arguments"></a>Argumentos
  
*str_expr1*  
   É uma expressão de cordas.  
  
*str_expr2*  
   É uma expressão de corda a ser comparada com o final de *str_expr1*.  
  
## <a name="return-types"></a>Tipos de retorno
  
  Devolve uma expressão booleana.  
  
## <a name="examples"></a>Exemplos
  
  O exemplo seguinte devolve "abc" termina com "b" e "bc".  
  
```sql
SELECT ENDSWITH("abc", "b") AS e1, ENDSWITH("abc", "bc") AS e2 
```  
  
 Aqui está o conjunto de resultados.  
  
```json
[{"e1": false, "e2": true}]  
```  

## <a name="remarks"></a>Observações

Esta função do sistema não utilizará o índice.

## <a name="next-steps"></a>Passos seguintes

- [Funções de corda Azure Cosmos DB](sql-query-string-functions.md)
- [Funcionamento do sistema Azure Cosmos DB](sql-query-system-functions.md)
- [Introdução ao Azure Cosmos DB](introduction.md)
