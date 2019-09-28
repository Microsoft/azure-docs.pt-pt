---
title: ARRAY_CONTAINS na linguagem de consulta Azure Cosmos DB
description: Saiba mais sobre a função do sistema SQL ARRAY_CONTAINS no Azure Cosmos DB.
author: ginamr
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 09/13/2019
ms.author: girobins
ms.custom: query-reference
ms.openlocfilehash: 247956ccc2718c9bf192b4d704a48014753c00dc
ms.sourcegitcommit: 7f6d986a60eff2c170172bd8bcb834302bb41f71
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 09/27/2019
ms.locfileid: "71348699"
---
# <a name="array_contains-azure-cosmos-db"></a>ARRAY_CONTAINS (Azure Cosmos DB)
Devolve um valor booleano que indica se a matriz contém o valor especificado. Você pode verificar se há uma correspondência parcial ou completa de um objeto usando uma expressão booliana dentro do comando. 

## <a name="syntax"></a>Sintaxe
  
```sql
ARRAY_CONTAINS (<arr_expr>, <expr> [, bool_expr])  
```  
  
## <a name="arguments"></a>Argumentos
  
*arr_expr*  
   É a expressão de matriz a ser pesquisada.  
  
*expr*  
   É a expressão a ser encontrada.  

*bool_expr*  
   É uma expressão booliana. Se for avaliada como ' true ' e se o valor de pesquisa especificado for um objeto, o comando verificará se há uma correspondência parcial (o objeto de pesquisa é um subconjunto de um dos objetos). Se ele for avaliado como ' false ', o comando verificará se há uma correspondência completa de todos os objetos dentro da matriz. O valor padrão, se não especificado, é false. 
  
## <a name="return-types"></a>Tipos de retorno
  
  Devolve um valor booleano.  
  
## <a name="examples"></a>Exemplos
  
  O exemplo a seguir como verificar a associação em uma matriz usando `ARRAY_CONTAINS`.  
  
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
  

## <a name="next-steps"></a>Passos seguintes

- [Funções de matriz Azure Cosmos DB](sql-query-array-functions.md)
- [Funções do sistema Azure Cosmos DB](sql-query-system-functions.md)
- [Introdução ao Azure Cosmos DB](introduction.md)
