---
title: StringToNull na linguagem de consulta de DD Do Cosmos de Azure
description: Saiba mais sobre a função do sistema SQL StringToNull em Azure Cosmos DB.
author: ginamr
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 03/03/2020
ms.author: girobins
ms.custom: query-reference
ms.openlocfilehash: 7e17547f88465103b61eabec04978ea806ffa2c9
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/28/2020
ms.locfileid: "78296444"
---
# <a name="stringtonull-azure-cosmos-db"></a>StringToNull (Azure Cosmos DB)
 Expressão de devoluções traduzida para nulo. Se a expressão não puder ser traduzida, regressa indefinido.  
  
## <a name="syntax"></a>Sintaxe
  
```sql
StringToNull(<str_expr>)  
```  
  
## <a name="arguments"></a>Argumentos
  
*str_expr*  
   É uma expressão de corda a ser analisada como uma expressão nula.
  
## <a name="return-types"></a>Tipos de retorno
  
  Devolve uma expressão nula ou indefinida.  
  
## <a name="examples"></a>Exemplos
  
  O exemplo que `StringToNull` se segue mostra como se comporta em diferentes tipos. 

Seguem-se exemplos com entrada válida.

 O Espaço Branco só é permitido antes ou depois de "nulo".

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

Nulo é sensível ao caso e deve ser escrito com todos os caracteres minúsculos, ou seja, "nulo".

```sql
SELECT    
    StringToNull("NULL"),
    StringToNull("Null")
```  
  
 Aqui está o conjunto de resultados.  
  
```json
[{}]
```  

A expressão aprovada será analisada como uma expressão nula; estas inputs não avaliam para escrever nulos e, assim, retornar indefinidos.

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
- [Funcionamento do sistema Azure Cosmos DB](sql-query-system-functions.md)
- [Introdução ao Azure Cosmos DB](introduction.md)
