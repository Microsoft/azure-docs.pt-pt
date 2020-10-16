---
title: Upper in Azure Cosmos DB linguagem de consulta
description: Saiba mais sobre a função do sistema SQL UPPER em Azure Cosmos DB.
author: ginamr
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 03/04/2020
ms.author: girobins
ms.custom: query-reference
ms.openlocfilehash: 5129b4fffafb6918f655263cac2f5564635acf36
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/09/2020
ms.locfileid: "78303975"
---
# <a name="upper-azure-cosmos-db"></a>UPPER (Azure Cosmos DB)
 Devolve uma expressão de corda após converter dados de caracteres minúsculos para maiúsculas.  

A função do sistema SUPERIOR não utiliza o índice. Se pretender fazer comparações frequentes de casos insensíveis, a função do sistema UPPER pode consumir uma quantidade significativa de RU's. Se for esse o caso, em vez de utilizar a função do sistema UPPER para normalizar os dados de cada vez para comparações, pode normalizar o invólucro após a inserção. Em seguida, uma consulta como SELECT * FROM c WHERE UPPER (c.name) = 'BOB' simplesmente torna-se SELECT * FROM c WHERE c.name = 'BOB'.

## <a name="syntax"></a>Sintaxe
  
```sql
UPPER(<str_expr>)  
```  
  
## <a name="arguments"></a>Argumentos
  
*str_expr*  
   É uma expressão de corda.  
  
## <a name="return-types"></a>Tipos de retorno
  
  Devolve uma expressão de corda.  
  
## <a name="examples"></a>Exemplos
  
  O exemplo a seguir mostra como usar `UPPER` numa consulta  
  
```sql
SELECT UPPER("Abc") AS upper  
```  
  
 Aqui está o conjunto de resultados.  
  
```json
[{"upper": "ABC"}]  
```

## <a name="remarks"></a>Observações

Esta função do sistema não utilizará o índice.

## <a name="next-steps"></a>Passos seguintes

- [Funções de corda Azure Cosmos DB](sql-query-string-functions.md)
- [Funciona O sistema Azure Cosmos DB](sql-query-system-functions.md)
- [Introdução ao Azure Cosmos DB](introduction.md)
