---
title: CHÃO em linguagem de consulta de Azure Cosmos DB
description: Conheça a função do sistema FLOOR SQL em Azure Cosmos DB para devolver o maior inteiro inferior ou igual à expressão numérica especificada
author: ginamr
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 09/13/2019
ms.author: girobins
ms.custom: query-reference
ms.openlocfilehash: 04dfa6a028cf7c44bf99c665b396d51d8a0f3cef
ms.sourcegitcommit: f915d8b43a3cefe532062ca7d7dbbf569d2583d8
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/05/2020
ms.locfileid: "78303193"
---
# <a name="floor-azure-cosmos-db"></a>PISO (Azure Cosmos DB)
 Devolve o maior número inteiro menor ou igual a expressão numérica especificada.  
  
## <a name="syntax"></a>Sintaxe
  
```sql
FLOOR (<numeric_expr>)  
```  
  
## <a name="arguments"></a>Argumentos
  
*numeric_expr*  
   É uma expressão numérica.  
  
## <a name="return-types"></a>Tipos de retorno
  
  Devolve uma expressão numérica.  
  
## <a name="examples"></a>Exemplos
  
  O exemplo seguinte mostra valores numéricos, negativos e nulos positivos com a função `FLOOR`.  
  
```sql
SELECT FLOOR(123.45) AS fl1, FLOOR(-123.45) AS fl2, FLOOR(0.0) AS fl3  
```  
  
 Aqui está o conjunto de resultados.  
  
```json
[{fl1: 123, fl2: -124, fl3: 0}]  
```

## <a name="remarks"></a>Observações

Esta função do sistema beneficiará de um índice de [alcance](index-policy.md#includeexclude-strategy).

## <a name="next-steps"></a>Passos seguintes

- [Funções matemáticas Azure Cosmos DB](sql-query-mathematical-functions.md)
- [Funcionamento do sistema Azure Cosmos DB](sql-query-system-functions.md)
- [Introdução ao Azure Cosmos DB](introduction.md)
