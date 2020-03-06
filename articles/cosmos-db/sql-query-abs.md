---
title: ABS em linguagem de consulta de Azure Cosmos DB
description: Saiba como funciona o sistema Absoluto (ABS) SQL em Azure Cosmos DB devolve o valor positivo da expressão numérica especificada
author: ginamr
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 03/04/2020
ms.author: girobins
ms.custom: query-reference
ms.openlocfilehash: 6d173827f2695cc20fa208d390731acf0edb3848
ms.sourcegitcommit: f915d8b43a3cefe532062ca7d7dbbf569d2583d8
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/05/2020
ms.locfileid: "78301102"
---
# <a name="abs-azure-cosmos-db"></a>ABS (Azure Cosmos DB)
 Devolve o valor absoluto (positivo) da expressão especificada numérico.  
  
## <a name="syntax"></a>Sintaxe
  
```sql
ABS (<numeric_expr>)  
```  
  
## <a name="arguments"></a>Argumentos
  
*numeric_expr*  
   É uma expressão numérica.  
  
## <a name="return-types"></a>Tipos de retorno
  
  Devolve uma expressão numérica.  
  
## <a name="examples"></a>Exemplos
  
  O exemplo seguinte mostra os resultados da utilização da função `ABS` em três números diferentes.  
  
```sql
SELECT ABS(-1) AS abs1, ABS(0) AS abs2, ABS(1) AS abs3 
```  
  
 Aqui está o conjunto de resultados.  
  
```json
[{abs1: 1, abs2: 0, abs3: 1}]  
```

## <a name="remarks"></a>Observações

Esta função do sistema beneficiará de um índice de [alcance](index-policy.md#includeexclude-strategy).

## <a name="next-steps"></a>Passos seguintes

- [Funções matemáticas Azure Cosmos DB](sql-query-mathematical-functions.md)
- [Funcionamento do sistema Azure Cosmos DB](sql-query-system-functions.md)
- [Introdução ao Azure Cosmos DB](introduction.md)
