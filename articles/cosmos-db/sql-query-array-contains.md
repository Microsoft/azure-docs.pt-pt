---
title: ARRAY_CONTAINS na linguagem de consulta DB do Azure Cosmos
description: Saiba como o sistema Array Contém SQL em Azure Cosmos DB devolve um Boolean indicando se a matriz contém o valor especificado
author: ginamr
ms.service: cosmos-db
ms.subservice: cosmosdb-sql
ms.topic: conceptual
ms.date: 09/13/2019
ms.author: girobins
ms.custom: query-reference
ms.openlocfilehash: ff88d66ee6d1ceee4f1cf4e7b6501ab323dbc79e
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/19/2021
ms.locfileid: "93332666"
---
# <a name="array_contains-azure-cosmos-db"></a>ARRAY_CONTAINS (Azure Cosmos DB)
[!INCLUDE[appliesto-sql-api](includes/appliesto-sql-api.md)]

Devolve um Boolean indicando se a matriz contém o valor especificado. Pode verificar se há uma correspondência parcial ou completa de um objeto utilizando uma expressão booleana dentro do comando. 

## <a name="syntax"></a>Sintaxe
  
```sql
ARRAY_CONTAINS (<arr_expr>, <expr> [, bool_expr])  
```  
  
## <a name="arguments"></a>Argumentos
  
*arr_expr*  
   É a expressão de matriz a ser revistada.  
  
*expr*  
   É a expressão a ser encontrada.  

*bool_expr*  
   É uma expressão booleana. Se avaliar a 'verdade' e se o valor de pesquisa especificado for um objeto, o comando verifica uma correspondência parcial (o objeto de pesquisa é um subconjunto de um dos objetos). Se avaliar a "falsa", o comando verifica uma correspondência completa de todos os objetos dentro da matriz. O valor predefinido se não especificado é falso. 
  
## <a name="return-types"></a>Tipos de retorno
  
  Devolve um valor Boolean.  
  
## <a name="examples"></a>Exemplos
  
  O exemplo a seguir como verificar a adesão numa matriz utilizando `ARRAY_CONTAINS` .  
  
```sql
SELECT   
           ARRAY_CONTAINS(["apples", "strawberries", "bananas"], "apples") AS b1,  
           ARRAY_CONTAINS(["apples", "strawberries", "bananas"], "mangoes") AS b2  
```  
  
 Aqui está o conjunto de resultados.  
  
```json
[{"b1": true, "b2": false}]  
```  

O exemplo seguinte como verificar uma correspondência parcial de um JSON numa matriz utilizando ARRAY_CONTAINS.  
  
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

Esta função do sistema beneficiará de um [índice de intervalo.](index-policy.md#includeexclude-strategy)

## <a name="next-steps"></a>Passos seguintes

- [Funções de matriz Azure Cosmos DB](sql-query-array-functions.md)
- [Funciona O sistema Azure Cosmos DB](sql-query-system-functions.md)
- [Introdução ao Azure Cosmos DB](introduction.md)
