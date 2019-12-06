---
title: LTRIM na linguagem de consulta Azure Cosmos DB
description: Saiba mais sobre a função de sistema SQL LTRIM no Azure Cosmos DB para retornar uma expressão de cadeia de caracteres depois de remover espaços em branco à esquerda
author: ginamr
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 09/13/2019
ms.author: girobins
ms.custom: query-reference
ms.openlocfilehash: 652204416c201ccca024aff0239fc10dcc3eb105
ms.sourcegitcommit: 9405aad7e39efbd8fef6d0a3c8988c6bf8de94eb
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 12/05/2019
ms.locfileid: "74870994"
---
# <a name="ltrim-azure-cosmos-db"></a>LTRIM (Azure Cosmos DB)
 Devolve uma expressão de cadeia de caracteres depois que ele remove espaços em branco à esquerda.  
  
## <a name="syntax"></a>Sintaxe
  
```sql
LTRIM(<str_expr>)  
```  
  
## <a name="arguments"></a>Argumentos
  
*str_expr*  
   É uma expressão de cadeia de caracteres.  
  
## <a name="return-types"></a>Tipos de retorno
  
  Devolve uma expressão de cadeia.  
  
## <a name="examples"></a>Exemplos
  
  O exemplo a seguir mostra como usar `LTRIM` dentro de uma consulta.  
  
```sql
SELECT LTRIM("  abc") AS l1, LTRIM("abc") AS l2, LTRIM("abc   ") AS l3 
```  
  
 Aqui está o conjunto de resultados.  
  
```json
[{"l1": "abc", "l2": "abc", "l3": "abc   "}]  
```  

## <a name="next-steps"></a>Passos seguintes

- [Funções de cadeia de caracteres Azure Cosmos DB](sql-query-string-functions.md)
- [Funções do sistema Azure Cosmos DB](sql-query-system-functions.md)
- [Introdução ao Azure Cosmos DB](introduction.md)
