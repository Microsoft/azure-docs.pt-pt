---
title: IS_NULL na linguagem de consulta do Azure Cosmos DB
description: Saiba mais sobre a função do sistema SQL IS_NULL em Azure Cosmos DB.
author: ginamr
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 09/13/2019
ms.author: girobins
ms.custom: query-reference
ms.openlocfilehash: 4dbf21c3052ddd5ebdd62925e65a854c47f59017
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/28/2020
ms.locfileid: "78303839"
---
# <a name="is_null-azure-cosmos-db"></a>IS_NULL (Azure Cosmos DB)
 Devolve um valor booleano indicando se o tipo da expressão especificada é nulo.  
  
## <a name="syntax"></a>Sintaxe
  
```sql
IS_NULL(<expr>)  
```  
  
## <a name="arguments"></a>Argumentos
  
*expr*  
   É qualquer expressão.  
  
## <a name="return-types"></a>Tipos de retorno
  
  Devolve uma expressão booleana.  
  
## <a name="examples"></a>Exemplos
  
  O exemplo seguinte verifica objetos de JSON Boolean, número, corda, nulo, objeto, matriz e tipos indefinidos utilizando a `IS_NULL` função.  
  
```sql
SELECT   
    IS_NULL(true) AS isNull1,   
    IS_NULL(1) AS isNull2,  
    IS_NULL("value") AS isNull3,   
    IS_NULL(null) AS isNull4,  
    IS_NULL({prop: "value"}) AS isNull5,   
    IS_NULL([1, 2, 3]) AS isNull6,  
    IS_NULL({prop: "value"}.prop2) AS isNull7  
```  
  
 Aqui está o conjunto de resultados.  
  
```json
[{"isNull1":false,"isNull2":false,"isNull3":false,"isNull4":true,"isNull5":false,"isNull6":false,"isNull7":false}]
```  

## <a name="remarks"></a>Observações

Esta função do sistema beneficiará de um índice de [alcance](index-policy.md#includeexclude-strategy).

## <a name="next-steps"></a>Passos seguintes

- [Funções de verificação de tipos Azure Cosmos DB](sql-query-type-checking-functions.md)
- [Funcionamento do sistema Azure Cosmos DB](sql-query-system-functions.md)
- [Introdução ao Azure Cosmos DB](introduction.md)
