---
title: StringToArray em linguagem de consulta de DD Do Cosmos azure
description: Saiba mais sobre a função do sistema SQL StringToArray em Azure Cosmos DB.
author: ginamr
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 03/03/2020
ms.author: girobins
ms.custom: query-reference
ms.openlocfilehash: 18acbd94fa3d717fc20b9e1020b9bf7c6db7744d
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/28/2020
ms.locfileid: "78302921"
---
# <a name="stringtoarray-azure-cosmos-db"></a>StringToArray (Azure Cosmos DB)
 Expressão de retorno traduzida para um Array. Se a expressão não puder ser traduzida, regressa indefinido.  
  
## <a name="syntax"></a>Sintaxe
  
```sql  
StringToArray(<str_expr>)  
```  
  
## <a name="arguments"></a>Argumentos
  
*str_expr*  
   É uma expressão de corda a ser analisada como uma expressão JSON Array. 
  
## <a name="return-types"></a>Tipos de retorno
  
  Devolve uma expressão de matriz ou indefinida. 
  
## <a name="remarks"></a>Observações
  Os valores das cordas aninhados devem ser escritos com duas citações para serem jSON válidos. Para mais detalhes sobre o formato JSON, consulte [json.org](https://json.org/)
  
## <a name="examples"></a>Exemplos
  
  O exemplo que `StringToArray` se segue mostra como se comporta em diferentes tipos. 
  
 Seguem-se exemplos com entrada válida.

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

Segue-se um exemplo de entrada inválida. 
   
 As cotações únicas dentro da matriz não são válidas JSON.
Mesmo que sejam válidos dentro de uma consulta, não irão analisar matrizes válidas. As cordas dentro da corda de matriz devem ser escapadas "[\\"\\"]" ou a citação circundante deve ser única "["]".

```sql
SELECT
    StringToArray("['5','6','7']")
```

Aqui está o conjunto de resultados.

```json
[{}]
```

Seguem-se exemplos de entrada inválida.
   
 A expressão aprovada será analisada como uma matriz JSON; os seguintes não avaliam a matriz de tipo e, assim, regressam indefinidos.
   
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

## <a name="remarks"></a>Observações

Esta função do sistema não utilizará o índice.

## <a name="next-steps"></a>Passos seguintes

- [Funções de corda Azure Cosmos DB](sql-query-string-functions.md)
- [Funcionamento do sistema Azure Cosmos DB](sql-query-system-functions.md)
- [Introdução ao Azure Cosmos DB](introduction.md)
