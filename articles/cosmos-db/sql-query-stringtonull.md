---
title: StringToNull em Azure Cosmos DB linguagem de consulta
description: Saiba mais sobre a função do sistema SQL StringToNull em Azure Cosmos DB.
author: ginamr
ms.service: cosmos-db
ms.subservice: cosmosdb-sql
ms.topic: conceptual
ms.date: 03/03/2020
ms.author: girobins
ms.custom: query-reference
ms.openlocfilehash: ee91ca3a32bacbf6590877f49bf74499fc00a8f9
ms.sourcegitcommit: fa90cd55e341c8201e3789df4cd8bd6fe7c809a3
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 11/04/2020
ms.locfileid: "93339590"
---
# <a name="stringtonull-azure-cosmos-db"></a>StringToNull (Azure Cosmos DB)
[!INCLUDE[appliesto-sql-api](includes/appliesto-sql-api.md)]

 Expressão de devoluções traduzida para nulo. Se a expressão não puder ser traduzida, retorna indefinidamente.  
  
## <a name="syntax"></a>Sintaxe
  
```sql
StringToNull(<str_expr>)  
```  
  
## <a name="arguments"></a>Argumentos
  
*str_expr*  
   É uma expressão de corda para ser analisada como uma expressão nula.
  
## <a name="return-types"></a>Tipos de retorno
  
  Devolve uma expressão nula ou indefinida.  
  
## <a name="examples"></a>Exemplos
  
  O exemplo a seguir mostra como `StringToNull` se comporta em diferentes tipos. 

Seguem-se exemplos com entrada válida.

 Whitespace só é permitido antes ou depois de "nulo".

```sql
SELECT 
    StringToNull("null") AS n1, 
    StringToNull("  null ") AS n2,
    IS_NULL(StringToNull("null   ")) AS n3
```  
  
 Aqui está o conjunto de resultados.  
  
```json
[{"n1": null, "n2": null, "n3": true}]
```  

Seguem-se exemplos com entrada inválida.

O nulo é sensível a caso e deve ser escrito com todos os caracteres minúsculos, ou seja, "nulos".

```sql
SELECT    
    StringToNull("NULL"),
    StringToNull("Null")
```  
  
 Aqui está o conjunto de resultados.  
  
```json
[{}]
```  

A expressão passada será analisada como uma expressão nula; estas entradas não avaliam para escrever nulo e, assim, devolver indefinidamente.

```sql
SELECT    
    StringToNull("true"), 
    StringToNull(false), 
    StringToNull(undefined),
    StringToNull(NaN) 
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
