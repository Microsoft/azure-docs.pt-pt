---
title: LICENCIATURAS em Linguagem de Consulta DB do Cosmos Azure
description: Saiba mais sobre a função do sistema DEGREES SQL em Azure Cosmos DB para devolver o ângulo correspondente em graus para um ângulo especificado em radianos
author: ginamr
ms.service: cosmos-db
ms.subservice: cosmosdb-sql
ms.topic: conceptual
ms.date: 03/03/2020
ms.author: girobins
ms.custom: query-reference
ms.openlocfilehash: 5c7aa5496533a42ad726f9ed8efd8ed7429375fc
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/29/2021
ms.locfileid: "93338905"
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
