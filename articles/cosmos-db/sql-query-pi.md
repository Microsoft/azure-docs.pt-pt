---
title: PI na linguagem de consulta Azure Cosmos DB
description: Saiba mais sobre a função do sistema SQL PI no Azure Cosmos DB.
author: ginamr
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 09/13/2019
ms.author: girobins
ms.custom: query-reference
ms.openlocfilehash: 27832008e8922e339a648985192a58b111555bc9
ms.sourcegitcommit: 7f6d986a60eff2c170172bd8bcb834302bb41f71
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 09/27/2019
ms.locfileid: "71349661"
---
# <a name="pi-azure-cosmos-db"></a>PI (Azure Cosmos DB)
 Devolve o valor da constante de PI.  
  
## <a name="syntax"></a>Sintaxe
  
```sql
PI ()  
```  
   
## <a name="return-types"></a>Tipos de retorno
  
  Devolve uma expressão numérica.  
  
## <a name="examples"></a>Exemplos
  
  O exemplo a seguir retorna o valor de `PI`.  
  
```sql
SELECT PI() AS pi 
```  
  
 Aqui está o conjunto de resultados.  
  
```json
[{"pi": 3.1415926535897931}]  
```  

## <a name="next-steps"></a>Passos seguintes

- [Funções matemáticas Azure Cosmos DB](sql-query-mathematical-functions.md)
- [Funções do sistema Azure Cosmos DB](sql-query-system-functions.md)
- [Introdução ao Azure Cosmos DB](introduction.md)
