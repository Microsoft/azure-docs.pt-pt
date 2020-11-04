---
title: StringToNumber em linguagem de consulta DB Azure Cosmos
description: Saiba mais sobre a função do sistema SQL StringToNumber em Azure Cosmos DB.
author: ginamr
ms.service: cosmos-db
ms.subservice: cosmosdb-sql
ms.topic: conceptual
ms.date: 03/03/2020
ms.author: girobins
ms.custom: query-reference
ms.openlocfilehash: f2622958a2315458ccd01da4aea7c6b75941e755
ms.sourcegitcommit: fa90cd55e341c8201e3789df4cd8bd6fe7c809a3
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 11/04/2020
ms.locfileid: "93339568"
---
# <a name="stringtonumber-azure-cosmos-db"></a>StringToNumber (Azure Cosmos DB)
[!INCLUDE[appliesto-sql-api](includes/appliesto-sql-api.md)]

 Devolução de expressão traduzida para um Número. Se a expressão não puder ser traduzida, retorna indefinidamente.  
  
## <a name="syntax"></a>Sintaxe
  
```sql
StringToNumber(<str_expr>)  
```  
  
## <a name="arguments"></a>Argumentos
  
*str_expr*  
   É uma expressão de corda a ser analisada como uma expressão JSON Number. Os números em JSON devem ser um número inteiro ou um ponto flutuante. Para mais detalhes sobre o formato JSON, consulte [json.org](https://json.org/)  
  
## <a name="return-types"></a>Tipos de retorno
  
  Devolve uma expressão numerada ou indefinida.  
  
## <a name="examples"></a>Exemplos
  
  O exemplo a seguir mostra como `StringToNumber` se comporta em diferentes tipos. 

Whitespace só é permitido antes ou depois do Número.

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

No JSON, um número válido deve ser um número inteiro ou um número de ponto flutuante.

```sql
SELECT   
    StringToNumber("0xF")
```  
  
 Aqui está o conjunto de resultados.  
  
```json
{{}}
```  

A expressão passada será analisada como uma expressão número; estas entradas não avaliam o número do tipo e, assim, devolvem indefinidamente. 

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
- [Funciona O sistema Azure Cosmos DB](sql-query-system-functions.md)
- [Introdução ao Azure Cosmos DB](introduction.md)
