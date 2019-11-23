---
title: StringToObject na linguagem de consulta Azure Cosmos DB
description: Saiba mais sobre a função do sistema SQL StringToObject no Azure Cosmos DB.
author: ginamr
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 09/13/2019
ms.author: girobins
ms.custom: query-reference
ms.openlocfilehash: f09c27458a630386664f3f6579cfeee0721d8be9
ms.sourcegitcommit: 7f6d986a60eff2c170172bd8bcb834302bb41f71
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 09/27/2019
ms.locfileid: "71349209"
---
# <a name="stringtoobject-azure-cosmos-db"></a>StringToObject (Azure Cosmos DB)
 Retorna a expressão convertida em um objeto. Se a expressão não puder ser convertida, retornará indefinido.  
  
## <a name="syntax"></a>Sintaxe
  
```sql
StringToObject(<str_expr>)  
```  
  
## <a name="arguments"></a>Argumentos
  
*str_expr*  
   É uma expressão de cadeia de caracteres a ser analisada como uma expressão de objeto JSON. Observe que os valores de cadeia de caracteres aninhados devem ser gravados com aspas duplas para serem válidos. Para obter detalhes sobre o formato JSON, consulte [JSON.org](https://json.org/)  
  
## <a name="return-types"></a>Tipos de retorno
  
  Retorna uma expressão de objeto ou indefinido.  
  
## <a name="examples"></a>Exemplos
  
  O exemplo a seguir mostra como `StringToObject` se comporta entre diferentes tipos. 
  
 Veja a seguir exemplos de entrada válida.

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

 Veja a seguir exemplos de entrada inválida.
Mesmo que eles sejam válidos em uma consulta, eles não serão analisados para objetos válidos. As cadeias dentro da cadeia de caracteres do objeto devem ter escape "{\\" a\\":\\" Str\\"}" ou a aspa ao redor deve ser única ' {"a": "Str"} '.

As aspas simples ao redor dos nomes de propriedade não são JSON válidos.

```sql
SELECT 
    StringToObject("{'a':[1,2,3]}")
```

Aqui está o conjunto de resultados.

```json
[{}]
```  

Nomes de propriedade sem aspas ao redor não são JSON válidos.

```sql
SELECT 
    StringToObject("{a:[1,2,3]}")
```

Aqui está o conjunto de resultados.

```json
[{}]
``` 

Veja a seguir exemplos de entrada inválida.

 A expressão passada será analisada como um objeto JSON; essas entradas não são avaliadas para o tipo Object e, portanto, retornam indefinidamente.

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

## <a name="next-steps"></a>Passos seguintes

- [Funções de cadeia de caracteres Azure Cosmos DB](sql-query-string-functions.md)
- [Funções do sistema Azure Cosmos DB](sql-query-system-functions.md)
- [Introdução ao Azure Cosmos DB](introduction.md)
