---
title: INFERIOR na linguagem de consulta Azure Cosmos DB
description: Saiba mais sobre a função de sistema SQL inferior no Azure Cosmos DB para retornar uma expressão de cadeia de caracteres após converter dados de caracteres maiúsculos em minúsculas
author: ginamr
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 09/13/2019
ms.author: girobins
ms.custom: query-reference
ms.openlocfilehash: 35efbb8d4d97ab52abb20487d15a80985946c499
ms.sourcegitcommit: c32050b936e0ac9db136b05d4d696e92fefdf068
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 01/08/2020
ms.locfileid: "75732608"
---
# <a name="lower-azure-cosmos-db"></a>INFERIOR (Azure Cosmos DB)
 Devolve uma expressão de cadeia de caracteres após a conversão de dados de caráter em maiúsculas em minúsculas.  

A função de sistema inferior não utiliza o índice. Se você planeja fazer comparações que não diferenciam maiúsculas de minúsculas, a função de sistema inferior pode consumir uma quantidade significativa de RU. Se esse for o caso, em vez de usar a função de sistema inferior para normalizar os dados a cada vez para comparações, você poderá normalizar a capitalização após a inserção. Em seguida, uma consulta como SELECT * de c, em que LOWER (c. Name) = ' Bob ' simplesmente se torna SELECT * de c onde c.name = ' Bob '.

## <a name="syntax"></a>Sintaxe
  
```sql
LOWER(<str_expr>)  
```  
  
## <a name="arguments"></a>Argumentos
  
*str_expr*  
   É uma expressão de cadeia de caracteres.  
  
## <a name="return-types"></a>Tipos de retorno
  
  Devolve uma expressão de cadeia.  
  
## <a name="examples"></a>Exemplos
  
  O exemplo a seguir mostra como usar `LOWER` em uma consulta.  
  
```sql
SELECT LOWER("Abc") AS lower
```  
  
 Aqui está o conjunto de resultados.  
  
```json
[{"lower": "abc"}]  
  
```  

## <a name="next-steps"></a>Passos seguintes

- [Funções de cadeia de caracteres Azure Cosmos DB](sql-query-string-functions.md)
- [Funções do sistema Azure Cosmos DB](sql-query-system-functions.md)
- [Introdução ao Azure Cosmos DB](introduction.md)
