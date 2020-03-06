---
title: UPPER em linguagem de consulta de Azure Cosmos DB
description: Saiba mais sobre a função do sistema SQL UPPER em Azure Cosmos DB.
author: ginamr
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 03/04/2020
ms.author: girobins
ms.custom: query-reference
ms.openlocfilehash: 5129b4fffafb6918f655263cac2f5564635acf36
ms.sourcegitcommit: f915d8b43a3cefe532062ca7d7dbbf569d2583d8
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/05/2020
ms.locfileid: "78303975"
---
# <a name="upper-azure-cosmos-db"></a>UPPER (Azure Cosmos DB)
 Devolve uma expressão de cadeia de caracteres após a conversão de dados de caráter em minúsculas em maiúsculas.  

A função do sistema SUPERIOR não utiliza o índice. Se planeia fazer comparações frequentes de casos insensíveis, a função do sistema SUPERIOR pode consumir uma quantidade significativa de RU's. Se for esse o caso, em vez de utilizar a função do sistema SUPERIOR para normalizar os dados de cada vez para comparações, pode normalizar o invólucro após a inserção. Em seguida, uma consulta como SELECT * From c WHERE UPPER (c.name) = 'BOB' torna-se simplesmente SELECT * DE c ONDE c.name = 'BOB'.

## <a name="syntax"></a>Sintaxe
  
```sql
UPPER(<str_expr>)  
```  
  
## <a name="arguments"></a>Argumentos
  
*str_expr*  
   É uma expressão de cordas.  
  
## <a name="return-types"></a>Tipos de retorno
  
  Devolve uma expressão de cadeia.  
  
## <a name="examples"></a>Exemplos
  
  O exemplo que se segue mostra como usá`UPPER` numa consulta  
  
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
- [Funcionamento do sistema Azure Cosmos DB](sql-query-system-functions.md)
- [Introdução ao Azure Cosmos DB](introduction.md)
