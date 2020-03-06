---
title: ARRAY_CONTAINS na linguagem de consulta do Azure Cosmos DB
description: Saiba como a função do sistema SQL contém sQL em Azure Cosmos DB devolve uma Boolean indicando se a matriz contém o valor especificado
author: ginamr
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 09/13/2019
ms.author: girobins
ms.custom: query-reference
ms.openlocfilehash: 47fe20888aa546e414b268b30c2e03580750a040
ms.sourcegitcommit: f915d8b43a3cefe532062ca7d7dbbf569d2583d8
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/05/2020
ms.locfileid: "78303482"
---
# <a name="array_contains-azure-cosmos-db"></a>ARRAY_CONTAINS (Azure Cosmos DB)
Devolve um valor booleano que indica se a matriz contém o valor especificado. Pode verificar se há uma correspondência parcial ou completa de um objeto utilizando uma expressão booleana dentro do comando. 

## <a name="syntax"></a>Sintaxe
  
```sql
ARRAY_CONTAINS (<arr_expr>, <expr> [, bool_expr])  
```  
  
## <a name="arguments"></a>Argumentos
  
*arr_expr*  
   É a expressão da matriz a ser revistada.  
  
*expr*  
   É a expressão a ser encontrada.  

*bool_expr*  
   É uma expressão booleana. Se avaliar como 'verdadeiro' e se o valor de pesquisa especificado for um objeto, o comando verifica uma correspondência parcial (o objeto de pesquisa é um subconjunto de um dos objetos). Se avaliar como 'falso', o comando verifica uma correspondência completa de todos os objetos dentro da matriz. O valor predefinido se não especificado é falso. 
  
## <a name="return-types"></a>Tipos de retorno
  
  Devolve um valor booleano.  
  
## <a name="examples"></a>Exemplos
  
  O exemplo seguinte como verificar a adesão numa matriz utilizando `ARRAY_CONTAINS`.  
  
```sql
SELECT   
           ARRAY_CONTAINS(["apples", "strawberries", "bananas"], "apples") AS b1,  
           ARRAY_CONTAINS(["apples", "strawberries", "bananas"], "mangoes") AS b2  
```  
  
 Aqui está o conjunto de resultados.  
  
```json
[{"b1": true, "b2": false}]  
```  

O exemplo a seguir como verificar a existência de uma correspondência parcial de um JSON numa matriz usando ARRAY_CONTAINS.  
  
```sql
SELECT  
    ARRAY_CONTAINS([{"name": "apples", "fresh": true}, {"name": "strawberries", "fresh": true}], {"name": "apples"}, true) AS b1, 
    ARRAY_CONTAINS([{"name": "apples", "fresh": true}, {"name": "strawberries", "fresh": true}], {"name": "apples"}) AS b2,
    ARRAY_CONTAINS([{"name": "apples", "fresh": true}, {"name": "strawberries", "fresh": true}], {"name": "mangoes"}, true) AS b3 
```  
  
 Aqui está o conjunto de resultados.  
  
```json
[{
  "b1": true,
  "b2": false,
  "b3": false
}]
```

## <a name="remarks"></a>Observações

Esta função do sistema beneficiará de um índice de [alcance](index-policy.md#includeexclude-strategy).

## <a name="next-steps"></a>Passos seguintes

- [Funções de matriz Azure Cosmos DB](sql-query-array-functions.md)
- [Funcionamento do sistema Azure Cosmos DB](sql-query-system-functions.md)
- [Introdução ao Azure Cosmos DB](introduction.md)
