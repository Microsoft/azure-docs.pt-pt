---
title: StringToNumber em linguagem de consulta DB Azure Cosmos
description: Saiba mais sobre a função do sistema SQL StringToNumber em Azure Cosmos DB.
author: ginamr
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 03/03/2020
ms.author: girobins
ms.custom: query-reference
ms.openlocfilehash: 5ca8d0c4a6d244823dda6f0f79a3cf5c743a12a9
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.contentlocale: pt-PT
ms.lasthandoff: 07/02/2020
ms.locfileid: "78296427"
---
# <a name="stringtonumber-azure-cosmos-db"></a>StringToNumber (Azure Cosmos DB)
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

## <a name="next-steps"></a>Próximos passos

- [Funções de corda Azure Cosmos DB](sql-query-string-functions.md)
- [Funciona O sistema Azure Cosmos DB](sql-query-system-functions.md)
- [Introdução ao Azure Cosmos DB](introduction.md)
