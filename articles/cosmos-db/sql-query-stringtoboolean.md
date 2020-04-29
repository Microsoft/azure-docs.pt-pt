---
title: StringToBoolean em linguagem de consulta de Db Do Mb
description: Saiba mais sobre a função do sistema SQL StringToBoolean em Azure Cosmos DB.
author: ginamr
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 03/03/2020
ms.author: girobins
ms.custom: query-reference
ms.openlocfilehash: ce11db91eff51e669f0917fbf34b1d560d0e9f07
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/28/2020
ms.locfileid: "78296546"
---
# <a name="stringtoboolean-azure-cosmos-db"></a>StringToBoolean (Azure Cosmos DB)
 Expressão de retorno traduzida para um Boolean. Se a expressão não puder ser traduzida, regressa indefinido.  
  
## <a name="syntax"></a>Sintaxe
  
```sql
StringToBoolean(<str_expr>)  
```  
  
## <a name="arguments"></a>Argumentos
  
*str_expr*  
   É uma expressão de corda a ser analisada como uma expressão booleana.  
  
## <a name="return-types"></a>Tipos de retorno
  
  Devolve uma expressão booleana ou indefinida.  
  
## <a name="examples"></a>Exemplos
  
  O exemplo que `StringToBoolean` se segue mostra como se comporta em diferentes tipos. 
 
 Seguem-se exemplos com entrada válida.

O espaço branco só é permitido antes ou depois de "verdadeiro"/"falso".

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

 Booleans são sensíveis ao caso e devem ser escritos com todos os caracteres minúsculos, ou seja, "verdadeiros" e "falsos".

```sql
SELECT 
    StringToBoolean("TRUE"),
    StringToBoolean("False")
```  

Aqui está o conjunto de resultados.  
  
```json
[{}]
``` 

A expressão aprovada será analisada como uma expressão booleana; estas inputs não avaliam para escrever Boolean e, assim, retornar indefinidos.

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
- [Funcionamento do sistema Azure Cosmos DB](sql-query-system-functions.md)
- [Introdução ao Azure Cosmos DB](introduction.md)
