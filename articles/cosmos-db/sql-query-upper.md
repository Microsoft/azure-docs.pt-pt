---
title: UPPER na linguagem de consulta Azure Cosmos DB
description: Saiba mais sobre a função do sistema SQL no Azure Cosmos DB.
author: ginamr
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 09/13/2019
ms.author: girobins
ms.custom: query-reference
ms.openlocfilehash: 9809a138a5e28eb069d545d39cfda815c915bd78
ms.sourcegitcommit: c32050b936e0ac9db136b05d4d696e92fefdf068
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 01/08/2020
ms.locfileid: "75728893"
---
# <a name="upper-azure-cosmos-db"></a>UPPER (Azure Cosmos DB)
 Devolve uma expressão de cadeia de caracteres após a conversão de dados de caráter em minúsculas em maiúsculas.  

A função de sistema superior não utiliza o índice. Se você planeja fazer comparações que não diferenciam maiúsculas de minúsculas, a função de sistema superior pode consumir uma quantidade significativa de RU. Se esse for o caso, em vez de usar a função de sistema superior para normalizar os dados a cada vez para comparações, você poderá normalizar a capitalização após a inserção. Em seguida, uma consulta como SELECT * de c, em que UPPER (c. Name) = ' BOB ' simplesmente se torna SELECT * de c onde c.name = ' BOB '.

## <a name="syntax"></a>Sintaxe
  
```sql
UPPER(<str_expr>)  
```  
  
## <a name="arguments"></a>Argumentos
  
*str_expr*  
   É uma expressão de cadeia de caracteres.  
  
## <a name="return-types"></a>Tipos de retorno
  
  Devolve uma expressão de cadeia.  
  
## <a name="examples"></a>Exemplos
  
  O exemplo a seguir mostra como usar `UPPER` em uma consulta  
  
```sql
SELECT UPPER("Abc") AS upper  
```  
  
 Aqui está o conjunto de resultados.  
  
```json
[{"upper": "ABC"}]  
```

## <a name="next-steps"></a>Passos seguintes

- [Funções de cadeia de caracteres Azure Cosmos DB](sql-query-string-functions.md)
- [Funções do sistema Azure Cosmos DB](sql-query-system-functions.md)
- [Introdução ao Azure Cosmos DB](introduction.md)
