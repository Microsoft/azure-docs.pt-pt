---
title: StringToArray em Azure Cosmos DB linguagem de consulta
description: Saiba mais sobre a função do sistema SQL StringToArray em Azure Cosmos DB.
author: ginamr
ms.service: cosmos-db
ms.subservice: cosmosdb-sql
ms.topic: conceptual
ms.date: 03/03/2020
ms.author: girobins
ms.custom: query-reference
ms.openlocfilehash: 7ae1f69e92e890daae528eb1f4dfb95f76560043
ms.sourcegitcommit: fa90cd55e341c8201e3789df4cd8bd6fe7c809a3
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 11/04/2020
ms.locfileid: "93337987"
---
# <a name="stringtoarray-azure-cosmos-db"></a>StringToArray (Azure Cosmos DB)
[!INCLUDE[appliesto-sql-api](includes/appliesto-sql-api.md)]

 Retorna a expressão traduzida para um Array. Se a expressão não puder ser traduzida, retorna indefinidamente.  
  
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
  Os valores das cordas aninhadas devem ser escritos com ações duplas para serem JSON válidos. Para mais informações sobre o formato JSON, consulte [json.org](https://json.org/). Esta função do sistema não utilizará o índice.
  
## <a name="examples"></a>Exemplos
  
  O exemplo a seguir mostra como `StringToArray` se comporta em diferentes tipos. 
  
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
   
 As cotações individuais dentro da matriz não são JSON válidas.
Apesar de serem válidos dentro de uma consulta, não irão analisar as matrizes válidas. As cordas dentro da corda da matriz devem ser escapados \\ "[" \\ ou a citação circundante deve ser única '[""]".

```sql
SELECT
    StringToArray("['5','6','7']")
```

Aqui está o conjunto de resultados.

```json
[{}]
```

Seguem-se exemplos de entrada inválida.
   
 A expressão passada será analisada como uma matriz JSON; o seguinte não avaliar para escrever matriz e, assim, devolver indefinidamente.
   
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

- [Funções de corda Azure Cosmos DB](sql-query-string-functions.md)
- [Funciona O sistema Azure Cosmos DB](sql-query-system-functions.md)
- [Introdução ao Azure Cosmos DB](introduction.md)
