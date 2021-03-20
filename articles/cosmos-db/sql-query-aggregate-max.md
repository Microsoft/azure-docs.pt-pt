---
title: MAX em Azure Cosmos DB linguagem de consulta
description: Saiba mais sobre a função do sistema MAX (MAX) SQL em Azure Cosmos DB.
author: timsander1
ms.service: cosmos-db
ms.subservice: cosmosdb-sql
ms.topic: conceptual
ms.date: 12/02/2020
ms.author: tisande
ms.custom: query-reference
ms.openlocfilehash: a3d8e3f2687a492461bdbbdd761e26cdb58f9e96
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/19/2021
ms.locfileid: "96553410"
---
# <a name="max-azure-cosmos-db"></a>MAX (Azure Cosmos DB)
[!INCLUDE[appliesto-sql-api](includes/appliesto-sql-api.md)]

Esta função agregada devolve o máximo dos valores na expressão.
  
## <a name="syntax"></a>Sintaxe
  
```sql
MAX(<scalar_expr>)  
```  
  
## <a name="arguments"></a>Argumentos

*scalar_expr*  
   É uma expressão escalar. 
  
## <a name="return-types"></a>Tipos de retorno
  
Devolve uma expressão escalar.  
  
## <a name="examples"></a>Exemplos
  
O exemplo a seguir devolve o valor máximo `propertyA` de:
  
```sql
SELECT MAX(c.propertyA)
FROM c
```  

## <a name="remarks"></a>Observações

Esta função do sistema beneficiará de um [índice de intervalo.](index-policy.md#includeexclude-strategy) Os argumentos `MAX` podem ser número, corda, booleano ou nulo. Quaisquer valores indefinidos serão ignorados.

Ao comparar dados de diferentes tipos, é utilizada a seguinte ordem prioritária (por ordem descendente):

- cadeia
- número
- boolean
- nulo

## <a name="next-steps"></a>Passos seguintes

- [Funções matemáticas em Azure Cosmos DB](sql-query-mathematical-functions.md)
- [Funciona o sistema em Azure Cosmos DB](sql-query-system-functions.md)
- [Funções agregadas em Azure Cosmos DB](sql-query-aggregate-functions.md)