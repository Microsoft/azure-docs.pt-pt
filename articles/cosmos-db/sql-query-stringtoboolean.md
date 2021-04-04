---
title: Lingueta de consulta StringToBoolean em Azure Cosmos DB
description: Saiba mais sobre a função do sistema SQL StringToBoolean em Azure Cosmos DB.
author: ginamr
ms.service: cosmos-db
ms.subservice: cosmosdb-sql
ms.topic: conceptual
ms.date: 03/03/2020
ms.author: girobins
ms.custom: query-reference
ms.openlocfilehash: 2ad7ca9e2e50395effcc50e776eee3f1740fbb7a
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/29/2021
ms.locfileid: "93337953"
---
# <a name="stringtoboolean-azure-cosmos-db"></a>StringToBoolean (Azure Cosmos DB)
[!INCLUDE[appliesto-sql-api](includes/appliesto-sql-api.md)]

 Retorna a expressão traduzida para um Boolean. Se a expressão não puder ser traduzida, retorna indefinidamente.  
  
## <a name="syntax"></a>Sintaxe
  
```sql
StringToBoolean(<str_expr>)  
```  
  
## <a name="arguments"></a>Argumentos
  
*str_expr*  
   É uma expressão de corda para ser analisada como uma expressão booleana.  
  
## <a name="return-types"></a>Tipos de retorno
  
  Devolve uma expressão booleana ou indefinida.  
  
## <a name="examples"></a>Exemplos
  
  O exemplo a seguir mostra como `StringToBoolean` se comporta em diferentes tipos. 
 
 Seguem-se exemplos com entrada válida.

Whitespace só é permitido antes ou depois de "verdadeiro"/"falso".

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

Seguem-se exemplos com entrada inválida.

 Os booleanos são sensíveis ao caso e devem ser escritos com todos os caracteres minúsculos, ou seja, "verdadeiros" e "falsos".

```sql
SELECT 
    StringToBoolean("TRUE"),
    StringToBoolean("False")
```  

Aqui está o conjunto de resultados.  
  
```json
[{}]
``` 

A expressão passada será analisada como uma expressão booleana; estas entradas não avaliam para escrever Boolean e, assim, devolver indefinidamente.

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

## <a name="remarks"></a>Observações

Esta função do sistema não utilizará o índice.

## <a name="next-steps"></a>Passos seguintes

- [Funções de corda Azure Cosmos DB](sql-query-string-functions.md)
- [Funciona O sistema Azure Cosmos DB](sql-query-system-functions.md)
- [Introdução ao Azure Cosmos DB](introduction.md)
