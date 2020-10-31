---
title: Linguado de perguntas StringToObject em Azure Cosmos DB
description: Saiba mais sobre a função do sistema SQL StringToObject em Azure Cosmos DB.
author: ginamr
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 03/03/2020
ms.author: girobins
ms.custom: query-reference
ms.openlocfilehash: e3492d463133c15bd5e6cabb8a173ff806712125
ms.sourcegitcommit: 3bdeb546890a740384a8ef383cf915e84bd7e91e
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/30/2020
ms.locfileid: "93097095"
---
# <a name="stringtoobject-azure-cosmos-db"></a>StringToObject (Azure Cosmos DB)
[!INCLUDE[appliesto-sql-api](includes/appliesto-sql-api.md)]

 Retorna a expressão traduzida para um Objeto. Se a expressão não puder ser traduzida, retorna indefinidamente.  
  
## <a name="syntax"></a>Sintaxe
  
```sql
StringToObject(<str_expr>)  
```  
  
## <a name="arguments"></a>Argumentos
  
*str_expr*  
   É uma expressão de corda para ser analisada como uma expressão de objeto JSON. Note que os valores das cordas aninhadas devem ser escritos com cotações duplas para serem válidos. Para mais detalhes sobre o formato JSON, consulte [json.org](https://json.org/)  
  
## <a name="return-types"></a>Tipos de retorno
  
  Devolve uma expressão de objeto ou indefinida.  
  
## <a name="examples"></a>Exemplos
  
  O exemplo a seguir mostra como `StringToObject` se comporta em diferentes tipos. 
  
 Seguem-se exemplos com entrada válida.

```sql
SELECT 
    StringToObject("{}") AS obj1, 
    StringToObject('{"A":[1,2,3]}') AS obj2,
    StringToObject('{"B":[{"b1":[5,6,7]},{"b2":8},{"b3":9}]}') AS obj3, 
    StringToObject("{\"C\":[{\"c1\":[5,6,7]},{\"c2\":8},{\"c3\":9}]}") AS obj4
``` 

Aqui está o conjunto de resultados.

```json
[{"obj1": {}, 
  "obj2": {"A": [1,2,3]}, 
  "obj3": {"B":[{"b1":[5,6,7]},{"b2":8},{"b3":9}]},
  "obj4": {"C":[{"c1":[5,6,7]},{"c2":8},{"c3":9}]}}]
```

 Seguem-se exemplos com entrada inválida.
Apesar de serem válidos dentro de uma consulta, não analisarão objetos válidos. As cordas dentro da cadeia do objeto devem ser escapados "{ \\ "a \\ "str \\ \\ "}" ou a citação circundante deve ser single '{"a": "str"}.

As cotações individuais em torno dos nomes de propriedade não são válidas JSON.

```sql
SELECT 
    StringToObject("{'a':[1,2,3]}")
```

Aqui está o conjunto de resultados.

```json
[{}]
```  

Os nomes de propriedade sem cotações circundantes não são válidos JSON.

```sql
SELECT 
    StringToObject("{a:[1,2,3]}")
```

Aqui está o conjunto de resultados.

```json
[{}]
``` 

Seguem-se exemplos com entrada inválida.

 A expressão passada será analisada como um objeto JSON; estas entradas não avaliam para escrever objeto e, assim, devolver indefinidamente.

```sql
SELECT 
    StringToObject("}"),
    StringToObject("{"),
    StringToObject("1"),
    StringToObject(NaN), 
    StringToObject(false), 
    StringToObject(undefined)
``` 
 
 Aqui está o conjunto de resultados.

```json
[{}]
```

## <a name="remarks"></a>Observações

Esta função do sistema não utilizará o índice.

## <a name="next-steps"></a>Passos seguintes

- [Funções de corda Azure Cosmos DB](sql-query-string-functions.md)
- [Funciona O sistema Azure Cosmos DB](sql-query-system-functions.md)
- [Introdução ao Azure Cosmos DB](introduction.md)
