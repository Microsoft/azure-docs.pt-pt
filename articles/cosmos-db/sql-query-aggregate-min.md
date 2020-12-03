---
title: MIN em Azure Cosmos DB linguagem de consulta
description: Saiba mais sobre a função do sistema MIN (MIN) SQL em Azure Cosmos DB.
author: timsander1
ms.service: cosmos-db
ms.subservice: cosmosdb-sql
ms.topic: conceptual
ms.date: 12/02/2020
ms.author: tisande
ms.custom: query-reference
ms.openlocfilehash: 679814822cca5a72bd261d3c8944863715e31f70
ms.sourcegitcommit: 65db02799b1f685e7eaa7e0ecf38f03866c33ad1
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 12/03/2020
ms.locfileid: "96555410"
---
# <a name="min-azure-cosmos-db"></a>MIN (Azure Cosmos DB)
[!INCLUDE[appliesto-sql-api](includes/appliesto-sql-api.md)]

Esta função agregada devolve o mínimo dos valores na expressão.
  
## <a name="syntax"></a>Sintaxe
  
```sql
MIN(<scalar_expr>)  
```  
  
## <a name="arguments"></a>Argumentos
  
*scalar_expr*  
   É uma expressão escalar. 
  
## <a name="return-types"></a>Tipos de retorno
  
Devolve uma expressão escalar.  
  
## <a name="examples"></a>Exemplos
  
O exemplo a seguir devolve o valor mínimo `propertyA` de:
  
```sql
SELECT MIN(c.propertyA)
FROM c
```  

## <a name="remarks"></a>Observações

Esta função do sistema beneficiará de um [índice de intervalo.](index-policy.md#includeexclude-strategy) Os argumentos `MIN` podem ser número, corda, booleano ou nulo. Quaisquer valores indefinidos serão ignorados.

Ao comparar dados de diferentes tipos, utiliza-se a seguinte ordem prioritária (por ordem ascendente):

- nulo
- boolean
- número
- string

## <a name="next-steps"></a>Passos seguintes

- [Funções matemáticas em Azure Cosmos DB](sql-query-mathematical-functions.md)
- [Funciona o sistema em Azure Cosmos DB](sql-query-system-functions.md)
- [Funções agregadas em Azure Cosmos DB](sql-query-aggregate-functions.md)