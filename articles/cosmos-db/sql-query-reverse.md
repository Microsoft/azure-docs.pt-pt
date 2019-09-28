---
title: Inverter na linguagem de consulta Azure Cosmos DB
description: Saiba mais sobre a função do sistema SQL inverso na Azure Cosmos DB.
author: ginamr
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 09/13/2019
ms.author: girobins
ms.custom: query-reference
ms.openlocfilehash: 8237918232bd8ba8edb2b8f71440ffd73a913334
ms.sourcegitcommit: 7f6d986a60eff2c170172bd8bcb834302bb41f71
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 09/27/2019
ms.locfileid: "71349558"
---
# <a name="reverse-azure-cosmos-db"></a>REVERse (Azure Cosmos DB)
 Devolve a ordem inversa de um valor de cadeia de caracteres.  
  
## <a name="syntax"></a>Sintaxe
  
```sql
REVERSE(<str_expr>)  
```  
  
## <a name="arguments"></a>Argumentos
  
*str_expr*  
   É uma expressão de cadeia de caracteres.  
  
## <a name="return-types"></a>Tipos de retorno
  
  Devolve uma expressão de cadeia.  
  
## <a name="examples"></a>Exemplos
  
  O exemplo a seguir mostra como usar `REVERSE` em uma consulta.  
  
```sql
SELECT REVERSE("Abc") AS reverse  
```  
  
 Aqui está o conjunto de resultados.  
  
```json
[{"reverse": "cbA"}]  
```  

## <a name="next-steps"></a>Passos seguintes

- [Funções de cadeia de caracteres Azure Cosmos DB](sql-query-string-functions.md)
- [Funções do sistema Azure Cosmos DB](sql-query-system-functions.md)
- [Introdução ao Azure Cosmos DB](introduction.md)
