---
title: CONCAT na linguagem de consulta Azure Cosmos DB
description: Saiba mais sobre como a função de sistema SQL CONCAT no Azure Cosmos DB retorna uma cadeia de caracteres que é o resultado da concatenação de dois ou mais valores de cadeia de caracteres
author: ginamr
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 09/13/2019
ms.author: girobins
ms.custom: query-reference
ms.openlocfilehash: adb8564d4addaaa3f4a383b32941549aed3d53bd
ms.sourcegitcommit: 9405aad7e39efbd8fef6d0a3c8988c6bf8de94eb
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 12/05/2019
ms.locfileid: "74871555"
---
# <a name="concat-azure-cosmos-db"></a>CONCAT (Azure Cosmos DB)
 Devolve uma cadeia que é o resultado da concatenação de dois ou mais valores de cadeia de caracteres.  
  
## <a name="syntax"></a>Sintaxe
  
```sql
CONCAT(<str_expr1>, <str_expr2> [, <str_exprN>])  
```  
  
## <a name="arguments"></a>Argumentos
  
*str_expr*  
   É uma expressão de cadeia de caracteres para concatenar aos outros valores. A função `CONCAT` requer pelo menos dois argumentos de *str_expr* .  
  
## <a name="return-types"></a>Tipos de retorno
  
  Devolve uma expressão de cadeia.  
  
## <a name="examples"></a>Exemplos
  
  O exemplo seguinte devolve a cadeia de caracteres concatenada dos valores especificados.  
  
```sql
SELECT CONCAT("abc", "def") AS concat  
```  
  
 Aqui está o conjunto de resultados.  
  
```json
[{"concat": "abcdef"}]  
```  
  

## <a name="next-steps"></a>Passos seguintes

- [Funções de cadeia de caracteres Azure Cosmos DB](sql-query-string-functions.md)
- [Funções do sistema Azure Cosmos DB](sql-query-system-functions.md)
- [Introdução ao Azure Cosmos DB](introduction.md)
