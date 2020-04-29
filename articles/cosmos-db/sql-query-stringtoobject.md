---
title: StringToObject em linguagem de consulta de DD Do MB
description: Saiba mais sobre a função do sistema SQL StringToObject em Azure Cosmos DB.
author: ginamr
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 03/03/2020
ms.author: girobins
ms.custom: query-reference
ms.openlocfilehash: c3e61d1efe20910d84ef4ff583d74982b3ea9f3d
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/28/2020
ms.locfileid: "78296386"
---
# <a name="stringtoobject-azure-cosmos-db"></a>StringToObject (Azure Cosmos DB)
 Expressão de retorno traduzida para um Objeto. Se a expressão não puder ser traduzida, regressa indefinido.  
  
## <a name="syntax"></a>Sintaxe
  
```sql
StringToObject(<str_expr>)  
```  
  
## <a name="arguments"></a>Argumentos
  
*str_expr*  
   É uma expressão de corda a ser analisada como uma expressão de objeto JSON. Note que os valores das cordas aninhados devem ser escritos com duas citações para serem válidos. Para mais detalhes sobre o formato JSON, consulte [json.org](https://json.org/)  
  
## <a name="return-types"></a>Tipos de retorno
  
  Devolve uma expressão de objeto ou indefinida.  
  
## <a name="examples"></a>Exemplos
  
  O exemplo que `StringToObject` se segue mostra como se comporta em diferentes tipos. 
  
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
Mesmo que sejam válidos dentro de uma consulta, não irão analisar objetos válidos. As cordas dentro da cadeia do objeto\\devem\\ser\\escapadas\\"{ "a "str"} ou a citação circundante deve ser única '{'a': "str"}".

As cotações únicas em torno dos nomes de propriedade não são válidas JSON.

```sql
SELECT 
    StringToObject("{'a':[1,2,3]}")
```

Aqui está o conjunto de resultados.

```json
[{}]
```  

Os nomes de propriedade sem cotações circundantes não são válidos jSON.

```sql
SELECT 
    StringToObject("{a:[1,2,3]}")
```

Aqui está o conjunto de resultados.

```json
[{}]
``` 

Seguem-se exemplos com entrada inválida.

 A expressão aprovada será analisada como um objeto JSON; estas inputs não avaliam para escrever objeto e, assim, retornar indefinidos.

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
- [Funcionamento do sistema Azure Cosmos DB](sql-query-system-functions.md)
- [Introdução ao Azure Cosmos DB](introduction.md)
