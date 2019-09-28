---
title: COMPRIMENTO na linguagem de consulta Azure Cosmos DB
description: Saiba mais sobre o comprimento da função do sistema SQL no Azure Cosmos DB.
author: ginamr
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 09/13/2019
ms.author: girobins
ms.custom: query-reference
ms.openlocfilehash: aa430152415b1662a73a388a03ba6d4721c730f0
ms.sourcegitcommit: 7f6d986a60eff2c170172bd8bcb834302bb41f71
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 09/27/2019
ms.locfileid: "71349752"
---
# <a name="length-azure-cosmos-db"></a>COMPRIMENTO (Azure Cosmos DB)
 Devolve o número de carateres da expressão de cadeia especificada.  
  
## <a name="syntax"></a>Sintaxe
  
```sql
LENGTH(<str_expr>)  
```  
  
## <a name="arguments"></a>Argumentos
  
*str_expr*  
   É a expressão de cadeia de caracteres a ser avaliada.  
  
## <a name="return-types"></a>Tipos de retorno
  
  Devolve uma expressão numérica.  
  
## <a name="examples"></a>Exemplos
  
  O exemplo seguinte devolve o comprimento de uma cadeia de caracteres.  
  
```sql
SELECT LENGTH("abc") AS len 
```  
  
 Aqui está o conjunto de resultados.  
  
```json
[{"len": 3}]  
```  

## <a name="next-steps"></a>Passos seguintes

- [Funções de cadeia de caracteres Azure Cosmos DB](sql-query-string-functions.md)
- [Funções do sistema Azure Cosmos DB](sql-query-system-functions.md)
- [Introdução ao Azure Cosmos DB](introduction.md)
