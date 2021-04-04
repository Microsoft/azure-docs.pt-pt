---
title: LOWER em Azure Cosmos DB linguagem de consulta
description: Saiba mais sobre a função do sistema LOWER SQL em Azure Cosmos DB para devolver uma expressão de corda após converter dados de caracteres maiúsculas para minúsculas
author: ginamr
ms.service: cosmos-db
ms.subservice: cosmosdb-sql
ms.topic: conceptual
ms.date: 09/13/2019
ms.author: girobins
ms.custom: query-reference
ms.openlocfilehash: dd1d9ddefd67cadb92632fd6db7a1fbd5a34f35a
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/29/2021
ms.locfileid: "93338444"
---
# <a name="lower-azure-cosmos-db"></a>LOWER (Azure Cosmos DB)
[!INCLUDE[appliesto-sql-api](includes/appliesto-sql-api.md)]

 Devolve uma expressão de corda após converter dados de caracteres maiúsculas para minúsculas.  

A função do sistema LOWER não utiliza o índice. Se planeia fazer comparações frequentes de casos insensíveis, a função do sistema LOWER pode consumir uma quantidade significativa de RU's. Se for esse o caso, em vez de utilizar a função do sistema LOWER para normalizar os dados de cada vez para comparações, pode normalizar o invólucro após a inserção. Em seguida, uma consulta como SELECT * FROM c WHERE LOWER (c.name) = 'bob' simplesmente torna-se SELECT * FROM c WHERE c.name = 'bob'.

## <a name="syntax"></a>Sintaxe
  
```sql
LOWER(<str_expr>)  
```  
  
## <a name="arguments"></a>Argumentos
  
*str_expr*  
   É uma expressão de corda.  
  
## <a name="return-types"></a>Tipos de retorno
  
  Devolve uma expressão de corda.  
  
## <a name="examples"></a>Exemplos
  
  O exemplo a seguir mostra como se usa `LOWER` numa consulta.  
  
```sql
SELECT LOWER("Abc") AS lower
```  
  
 Aqui está o conjunto de resultados.  
  
```json
[{"lower": "abc"}]  
  
```  

## <a name="remarks"></a>Observações

Esta função do sistema não utilizará o índice.

## <a name="next-steps"></a>Passos seguintes

- [Funções de corda Azure Cosmos DB](sql-query-string-functions.md)
- [Funciona O sistema Azure Cosmos DB](sql-query-system-functions.md)
- [Introdução ao Azure Cosmos DB](introduction.md)
