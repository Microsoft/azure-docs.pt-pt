---
title: StringToNumber na linguagem de consulta do Azure Cosmos DB
description: Saiba mais sobre a função do sistema SQL StringToNumber em Azure Cosmos DB.
author: ginamr
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 03/03/2020
ms.author: girobins
ms.custom: query-reference
ms.openlocfilehash: 5ca8d0c4a6d244823dda6f0f79a3cf5c743a12a9
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/28/2020
ms.locfileid: "78296427"
---
# <a name="stringtonumber-azure-cosmos-db"></a>StringToNumber (Azure Cosmos DB)
 Expressão de devoluções traduzida para um número. Se a expressão não puder ser traduzida, regressa indefinido.  
  
## <a name="syntax"></a>Sintaxe
  
```sql
StringToNumber(<str_expr>)  
```  
  
## <a name="arguments"></a>Argumentos
  
*str_expr*  
   É uma expressão de corda a ser analisada como uma expressão json número. Os números em JSON devem ser inteiros ou um ponto flutuante. Para mais detalhes sobre o formato JSON, consulte [json.org](https://json.org/)  
  
## <a name="return-types"></a>Tipos de retorno
  
  Devolve uma expressão de número ou indefinida.  
  
## <a name="examples"></a>Exemplos
  
  O exemplo que `StringToNumber` se segue mostra como se comporta em diferentes tipos. 

O Espaço Branco só é permitido antes ou depois do número.

```sql
SELECT 
    StringToNumber("1.000000") AS num1, 
    StringToNumber("3.14") AS num2,
    StringToNumber("   60   ") AS num3, 
    StringToNumber("-1.79769e+308") AS num4
```  
  
 Aqui está o conjunto de resultados.  
  
```json
{{"num1": 1, "num2": 3.14, "num3": 60, "num4": -1.79769e+308}}
```  

Em JSON, um número válido deve ser um número inteiro ou um ponto flutuante.

```sql
SELECT   
    StringToNumber("0xF")
```  
  
 Aqui está o conjunto de resultados.  
  
```json
{{}}
```  

A expressão aprovada será analisada como expressão número; estas inputs não avaliam o tipo Número e, portanto, regressam indefinidas. 

```sql
SELECT 
    StringToNumber("99     54"),   
    StringToNumber(undefined),
    StringToNumber("false"),
    StringToNumber(false),
    StringToNumber(" "),
    StringToNumber(NaN)
```  
  
 Aqui está o conjunto de resultados.  
  
```json
{{}}
```  

## <a name="remarks"></a>Observações

Esta função do sistema não utilizará o índice.

## <a name="next-steps"></a>Passos seguintes

- [Funções de corda Azure Cosmos DB](sql-query-string-functions.md)
- [Funcionamento do sistema Azure Cosmos DB](sql-query-system-functions.md)
- [Introdução ao Azure Cosmos DB](introduction.md)
