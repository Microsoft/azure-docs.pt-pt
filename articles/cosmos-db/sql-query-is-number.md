---
title: IS_NUMBER na linguagem de consulta do Azure Cosmos DB
description: Saiba mais sobre a função do sistema SQL IS_NUMBER em Azure Cosmos DB.
author: ginamr
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 09/13/2019
ms.author: girobins
ms.custom: query-reference
ms.openlocfilehash: c019424241bc07d5a5d2cc19a64685c476f94548
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/28/2020
ms.locfileid: "78303822"
---
# <a name="is_number-azure-cosmos-db"></a>IS_NUMBER (Azure Cosmos DB)
 Devolve um valor booleano indicando se o tipo da expressão especificada é um número.  
  
## <a name="syntax"></a>Sintaxe
  
```sql
IS_NUMBER(<expr>)  
```  
  
## <a name="arguments"></a>Argumentos
  
*expr*  
   É qualquer expressão.  
  
## <a name="return-types"></a>Tipos de retorno
  
  Devolve uma expressão booleana.  
  
## <a name="examples"></a>Exemplos
  
  O exemplo seguinte verifica objetos de JSON Boolean, número, corda, nulo, objeto, matriz e tipos indefinidos utilizando a `IS_NUMBER` função.  
  
```sql
SELECT   
    IS_NUMBER(true) AS isNum1,   
    IS_NUMBER(1) AS isNum2,  
    IS_NUMBER("value") AS isNum3,   
    IS_NUMBER(null) AS isNum4,  
    IS_NUMBER({prop: "value"}) AS isNum5,   
    IS_NUMBER([1, 2, 3]) AS isNum6,  
    IS_NUMBER({prop: "value"}.prop2) AS isNum7  
```  
  
 Aqui está o conjunto de resultados.  
  
```json
[{"isNum1":false,"isNum2":true,"isNum3":false,"isNum4":false,"isNum5":false,"isNum6":false,"isNum7":false}]  
```  

## <a name="remarks"></a>Observações

Esta função do sistema beneficiará de um índice de [alcance](index-policy.md#includeexclude-strategy).

## <a name="next-steps"></a>Passos seguintes

- [Funções de verificação de tipos Azure Cosmos DB](sql-query-type-checking-functions.md)
- [Funcionamento do sistema Azure Cosmos DB](sql-query-system-functions.md)
- [Introdução ao Azure Cosmos DB](introduction.md)
