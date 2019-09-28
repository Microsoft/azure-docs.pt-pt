---
title: StringToArray na linguagem de consulta Azure Cosmos DB
description: Saiba mais sobre a função do sistema SQL StringToArray no Azure Cosmos DB.
author: ginamr
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 09/13/2019
ms.author: girobins
ms.custom: query-reference
ms.openlocfilehash: 2d1f90da50950ac6ff4f87ffe96ebad9f3d811cc
ms.sourcegitcommit: 7f6d986a60eff2c170172bd8bcb834302bb41f71
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 09/27/2019
ms.locfileid: "71349274"
---
# <a name="stringtoarray-azure-cosmos-db"></a>StringToArray (Azure Cosmos DB)
 Retorna a expressão convertida em uma matriz. Se a expressão não puder ser convertida, retornará indefinido.  
  
## <a name="syntax"></a>Sintaxe
  
```sql  
StringToArray(<str_expr>)  
```  
  
## <a name="arguments"></a>Argumentos
  
*str_expr*  
   É uma expressão de cadeia de caracteres a ser analisada como uma expressão de matriz JSON. 
  
## <a name="return-types"></a>Tipos de retorno
  
  Retorna uma expressão de matriz ou indefinido. 
  
## <a name="remarks"></a>Observações
  Valores de cadeia de caracteres aninhados devem ser gravados com aspas duplas para serem JSON válidos. Para obter detalhes sobre o formato JSON, consulte [JSON.org](https://json.org/)
  
## <a name="examples"></a>Exemplos
  
  O exemplo a seguir mostra como `StringToArray` se comporta entre diferentes tipos. 
  
 Veja a seguir exemplos de entrada válida.

```sql
SELECT 
    StringToArray('[]') AS a1, 
    StringToArray("[1,2,3]") AS a2,
    StringToArray("[\"str\",2,3]") AS a3,
    StringToArray('[["5","6","7"],["8"],["9"]]') AS a4,
    StringToArray('[1,2,3, "[4,5,6]",[7,8]]') AS a5
```

Aqui está o conjunto de resultados.

```json
[{"a1": [], "a2": [1,2,3], "a3": ["str",2,3], "a4": [["5","6","7"],["8"],["9"]], "a5": [1,2,3,"[4,5,6]",[7,8]]}]
```

Veja a seguir um exemplo de entrada inválida. 
   
 Aspas simples dentro da matriz não são JSON válidos.
Mesmo que eles sejam válidos em uma consulta, eles não serão analisados em matrizes válidas. As cadeias de caracteres na cadeia de matriz devem ter escape "[\\" \\ "]" ou a aspa ao redor deve ser única ' [""] '.

```sql
SELECT
    StringToArray("['5','6','7']")
```

Aqui está o conjunto de resultados.

```json
[{}]
```

Veja a seguir exemplos de entrada inválida.
   
 A expressão passada será analisada como uma matriz JSON; o seguinte não é avaliado para a matriz de tipos e, portanto, retorna indefinido.
   
```sql
SELECT
    StringToArray("["),
    StringToArray("1"),
    StringToArray(NaN),
    StringToArray(false),
    StringToArray(undefined)
```

Aqui está o conjunto de resultados.

```json
[{}]
```

## <a name="next-steps"></a>Passos seguintes

- [Funções de cadeia de caracteres Azure Cosmos DB](sql-query-string-functions.md)
- [Funções do sistema Azure Cosmos DB](sql-query-system-functions.md)
- [Introdução ao Azure Cosmos DB](introduction.md)
