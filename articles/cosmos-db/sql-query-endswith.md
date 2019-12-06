---
title: ENDSWITH na linguagem de consulta Azure Cosmos DB
description: Saiba mais sobre a função de sistema SQL ENDSWITH em Azure Cosmos DB para retornar um booliano que indica se a primeira expressão de cadeia de caracteres termina com a segunda
author: ginamr
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 09/13/2019
ms.author: girobins
ms.custom: query-reference
ms.openlocfilehash: 6b3e692877faab8a8d507a44068d4cdfdc73a916
ms.sourcegitcommit: 9405aad7e39efbd8fef6d0a3c8988c6bf8de94eb
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 12/05/2019
ms.locfileid: "74873357"
---
# <a name="endswith-azure-cosmos-db"></a>ENDSWITH (Azure Cosmos DB)
 Retorna um Booleano indicando se a primeira expressão de cadeia termina com a segunda.  
  
## <a name="syntax"></a>Sintaxe
  
```sql
ENDSWITH(<str_expr1>, <str_expr2>)  
```  
  
## <a name="arguments"></a>Argumentos
  
*str_expr1*  
   É uma expressão de cadeia de caracteres.  
  
*str_expr2*  
   É uma expressão de cadeia de caracteres a ser comparada ao final de *str_expr1*.  
  
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

## <a name="next-steps"></a>Passos seguintes

- [Funções de cadeia de caracteres Azure Cosmos DB](sql-query-string-functions.md)
- [Funções do sistema Azure Cosmos DB](sql-query-system-functions.md)
- [Introdução ao Azure Cosmos DB](introduction.md)
