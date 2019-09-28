---
title: StringToNull na linguagem de consulta Azure Cosmos DB
description: Saiba mais sobre a função do sistema SQL StringToNull no Azure Cosmos DB.
author: ginamr
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 09/13/2019
ms.author: girobins
ms.custom: query-reference
ms.openlocfilehash: c7ea3ca82cadb1351d8581f3d652ce060d67969b
ms.sourcegitcommit: 7f6d986a60eff2c170172bd8bcb834302bb41f71
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 09/27/2019
ms.locfileid: "71349240"
---
# <a name="stringtonull-azure-cosmos-db"></a>StringToNull (Azure Cosmos DB)
 Retorna a expressão convertida em NULL. Se a expressão não puder ser convertida, retornará indefinido.  
  
## <a name="syntax"></a>Sintaxe
  
```sql
StringToNull(<str_expr>)  
```  
  
## <a name="arguments"></a>Argumentos
  
*str_expr*  
   É uma expressão de cadeia de caracteres a ser analisada como uma expressão nula.
  
## <a name="return-types"></a>Tipos de retorno
  
  Retorna uma expressão nula ou indefinido.  
  
## <a name="examples"></a>Exemplos
  
  O exemplo a seguir mostra como `StringToNull` se comporta entre diferentes tipos. 

Veja a seguir exemplos de entrada válida.

 O espaço em branco é permitido somente antes ou depois de "NULL".

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

Veja a seguir exemplos de entrada inválida.

NULL diferencia maiúsculas de minúsculas e deve ser escrito com todos os caracteres minúsculos, ou seja, "NULL".

```sql
SELECT    
    StringToNull("NULL"),
    StringToNull("Null")
```  
  
 Aqui está o conjunto de resultados.  
  
```json
[{}]
```  

A expressão passada será analisada como uma expressão nula; essas entradas não são avaliadas como tipo NULL e, portanto, retornam indefinidamente.

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

## <a name="next-steps"></a>Passos seguintes

- [Funções de cadeia de caracteres Azure Cosmos DB](sql-query-string-functions.md)
- [Funções do sistema Azure Cosmos DB](sql-query-system-functions.md)
- [Introdução ao Azure Cosmos DB](introduction.md)
