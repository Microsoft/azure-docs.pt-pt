---
title: StringToBoolean na linguagem de consulta Azure Cosmos DB
description: Saiba mais sobre a função do sistema SQL StringToBoolean no Azure Cosmos DB.
author: ginamr
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 09/13/2019
ms.author: girobins
ms.custom: query-reference
ms.openlocfilehash: 8719857dca16585a045f8174dbac8df455f38f38
ms.sourcegitcommit: 7f6d986a60eff2c170172bd8bcb834302bb41f71
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 09/27/2019
ms.locfileid: "71349266"
---
# <a name="stringtoboolean-azure-cosmos-db"></a>StringToBoolean (Azure Cosmos DB)
 Retorna a expressão convertida em um booliano. Se a expressão não puder ser convertida, retornará indefinido.  
  
## <a name="syntax"></a>Sintaxe
  
```sql
StringToBoolean(<str_expr>)  
```  
  
## <a name="arguments"></a>Argumentos
  
*str_expr*  
   É uma expressão de cadeia de caracteres a ser analisada como uma expressão booliana.  
  
## <a name="return-types"></a>Tipos de retorno
  
  Retorna uma expressão booliana ou indefinida.  
  
## <a name="examples"></a>Exemplos
  
  O exemplo a seguir mostra como `StringToBoolean` se comporta entre diferentes tipos. 
 
 Veja a seguir exemplos de entrada válida.

O espaço em branco é permitido somente antes ou depois de "true"/"false".

```sql
SELECT 
    StringToBoolean("true") AS b1, 
    StringToBoolean("    false") AS b2,
    StringToBoolean("false    ") AS b3
```  
  
 Aqui está o conjunto de resultados.  
  
```json
[{"b1": true, "b2": false, "b3": false}]
```  

Veja a seguir exemplos de entrada inválida.

 Os boolianos diferenciam maiúsculas de minúsculas e devem ser escritos com todos os caracteres minúsculos, ou seja, "true" e "false".

```sql
SELECT 
    StringToBoolean("TRUE"),
    StringToBoolean("False")
```  

Aqui está o conjunto de resultados.  
  
```json
[{}]
``` 

A expressão passada será analisada como uma expressão booliana; essas entradas não são avaliadas como tipo booliano e, portanto, retornam indefinidamente.

```sql
SELECT 
    StringToBoolean("null"),
    StringToBoolean(undefined),
    StringToBoolean(NaN), 
    StringToBoolean(false), 
    StringToBoolean(true)
```  

Aqui está o conjunto de resultados.  
  
```json
[{}]
```  

## <a name="next-steps"></a>Passos seguintes

- [Funções de cadeia de caracteres Azure Cosmos DB](sql-query-string-functions.md)
- [Funções do sistema Azure Cosmos DB](sql-query-system-functions.md)
- [Introdução ao Azure Cosmos DB](introduction.md)
