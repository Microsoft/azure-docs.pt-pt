---
title: LICENCIATURAS em Linguagem de Consulta DB do Cosmos Azure
description: Saiba mais sobre a função do sistema DEGREES SQL em Azure Cosmos DB para devolver o ângulo correspondente em graus para um ângulo especificado em radianos
author: ginamr
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 03/03/2020
ms.author: girobins
ms.custom: query-reference
ms.openlocfilehash: 991d83a329603342975a8186fe704afc53813e08
ms.sourcegitcommit: 3bdeb546890a740384a8ef383cf915e84bd7e91e
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/30/2020
ms.locfileid: "93095769"
---
# <a name="degrees-azure-cosmos-db"></a>GRAUS (Azure Cosmos DB)
[!INCLUDE[appliesto-sql-api](includes/appliesto-sql-api.md)]

 Devolve o ângulo correspondente em graus para um ângulo especificado em radiais.  
  
## <a name="syntax"></a>Sintaxe
  
```sql
DEGREES (<numeric_expr>)  
```  
  
## <a name="arguments"></a>Argumentos
  
*numeric_expr*  
   É uma expressão numérica.  
  
## <a name="return-types"></a>Tipos de retorno
  
  Devolve uma expressão numérica.  
  
## <a name="examples"></a>Exemplos
  
  O exemplo a seguir devolve o número de graus num ângulo de raios PI/2.  
  
```sql
SELECT DEGREES(PI()/2) AS degrees  
```  
  
 Aqui está o conjunto de resultados.  
  
```json
[{"degrees": 90}]  
```  

## <a name="remarks"></a>Observações

Esta função do sistema não utilizará o índice.

## <a name="next-steps"></a>Passos seguintes

- [Funções matemáticas Azure Cosmos DB](sql-query-mathematical-functions.md)
- [Funciona O sistema Azure Cosmos DB](sql-query-system-functions.md)
- [Introdução ao Azure Cosmos DB](introduction.md)
