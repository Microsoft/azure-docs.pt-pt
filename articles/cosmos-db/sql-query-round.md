---
title: ROUND in Azure Cosmos DB linguagem de consulta
description: Saiba mais sobre a função do sistema SQL ROUND em Azure Cosmos DB.
author: ginamr
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 09/13/2019
ms.author: girobins
ms.custom: query-reference
ms.openlocfilehash: b6aac5a963d0f58a3b21b9fb0958793169a3d444
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 07/02/2020
ms.locfileid: "78302122"
---
# <a name="round-azure-cosmos-db"></a>ROUND (Azure Cosmos DB)
 Devolve um valor numérico, arredondado para o valor inteiro mais próximo.  
  
## <a name="syntax"></a>Sintaxe
  
```sql
ROUND(<numeric_expr>)  
```  
  
## <a name="arguments"></a>Argumentos
  
*numeric_expr*  
   É uma expressão numérica.  
  
## <a name="return-types"></a>Tipos de retorno
  
  Devolve uma expressão numérica.  
  
## <a name="remarks"></a>Observações
  
  A operação de arredondamento realizada segue-se a meio ponto, afastando-se do zero. Se a entrada for uma expressão numérica que cai exatamente entre dois inteiros, então o resultado será o valor inteiro mais próximo de zero.  
  
  |<numeric_expr>|Arredondado|
  |-|-|
  |-6.5000|-7|
  |-0.5|-1|
  |0,5|1|
  |6.5000|7||
  
## <a name="examples"></a>Exemplos
  
  O exemplo a seguir completa os seguintes números positivos e negativos para o número inteiro mais próximo.  
  
```sql
SELECT ROUND(2.4) AS r1, ROUND(2.6) AS r2, ROUND(2.5) AS r3, ROUND(-2.4) AS r4, ROUND(-2.6) AS r5  
```  
  
  Aqui está o conjunto de resultados.  
  
```json
[{r1: 2, r2: 3, r3: 3, r4: -2, r5: -3}]  
```  

## <a name="remarks"></a>Observações

Esta função do sistema beneficiará de um [índice de intervalo.](index-policy.md#includeexclude-strategy)

## <a name="next-steps"></a>Próximos passos

- [Funções matemáticas Azure Cosmos DB](sql-query-mathematical-functions.md)
- [Funciona O sistema Azure Cosmos DB](sql-query-system-functions.md)
- [Introdução ao Azure Cosmos DB](introduction.md)
