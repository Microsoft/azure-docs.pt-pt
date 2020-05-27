---
title: ComeçaCom em linguagem de consulta de Azure Cosmos DB
description: Saiba mais sobre a função do sistema SQL STARTWITH em Azure Cosmos DB.
author: ginamr
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 05/20/2020
ms.author: girobins
ms.custom: query-reference
ms.openlocfilehash: c64efb92de00291e6381e30af24e76df2b38aee0
ms.sourcegitcommit: 1f25aa993c38b37472cf8a0359bc6f0bf97b6784
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 05/26/2020
ms.locfileid: "83847119"
---
# <a name="startswith-azure-cosmos-db"></a>STARTWITH (Azure Cosmos DB)

 Devolve uma Boolean indicando se a primeira expressão de corda começa com a segunda.  
  
## <a name="syntax"></a>Sintaxe
  
```sql
STARTSWITH(<str_expr1>, <str_expr2> [, <bool_expr>])  
```  
  
## <a name="arguments"></a>Argumentos
  
*str_expr1*  
   É uma expressão de cordas.
  
*str_expr2*  
   É uma expressão de corda a ser comparada com o início de *str_expr1*.

*bool_expr* Valor opcional para ignorar caso. Quando definido como verdadeiro, a STARTWITH fará uma pesquisa insensível de caso. Quando não especificado, este valor é falso.

## <a name="return-types"></a>Tipos de retorno
  
  Devolve uma expressão booleana.  
  
## <a name="examples"></a>Exemplos
  
O exemplo seguinte verifica se a corda "abc" começa com "b" e "A".  
  
```sql
SELECT STARTSWITH("abc", "b", false) AS s1, STARTSWITH("abc", "A", false) AS s2, STARTSWITH("abc", "A", true) AS s3
```  
  
 Aqui está o conjunto de resultados.  
  
```json
[
    {
        "s1": false,
        "s2": false,
        "s3": true
    }
]
```  

## <a name="remarks"></a>Observações

Esta função do sistema beneficiará de um índice de [alcance](index-policy.md#includeexclude-strategy).

## <a name="next-steps"></a>Passos seguintes

- [Funções de corda Azure Cosmos DB](sql-query-string-functions.md)
- [Funcionamento do sistema Azure Cosmos DB](sql-query-system-functions.md)
- [Introdução ao Azure Cosmos DB](introduction.md)
