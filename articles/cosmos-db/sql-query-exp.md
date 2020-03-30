---
title: EXP em linguagem de consulta de Azure Cosmos DB
description: Conheça a função do sistema Exponent (EXP) SQL em Azure Cosmos DB para devolver o valor exponencial da expressão numérica especificada
author: ginamr
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 09/13/2019
ms.author: girobins
ms.custom: query-reference
ms.openlocfilehash: 76d614264124e1ce4138663b702ff6d899b3aa4e
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/27/2020
ms.locfileid: "74873323"
---
# <a name="exp-azure-cosmos-db"></a>EXP (Azure Cosmos DB)
 Devolve o valor exponencial da expressão numérica especificada.  
  
## <a name="syntax"></a>Sintaxe
  
```sql
EXP (<numeric_expr>)  
```  
  
## <a name="arguments"></a>Argumentos
  
*numeric_expr*  
   É uma expressão numérica.  
  
## <a name="return-types"></a>Tipos de retorno
  
  Devolve uma expressão numérica.  
  
## <a name="remarks"></a>Observações
  
  A constante **e** (2.718281...), é a base de logarithms naturais.  
  
  O expoente de um número é o constante **e** elevado ao poder do número. Por exemplo, EXP(1.0) = e^1.0 = 2.71828182845905 e EXP(10) = e^10 = 22026.4657948067.  
  
  O exponencial do logarithm natural de um número é o próprio número: EXP (LOG (n)) = n. E o logarithm natural do exponencial de um número é o próprio número: LOG (EXP (n)) = n.  
  
## <a name="examples"></a>Exemplos
  
  O exemplo seguinte declara uma variável e devolve o valor exponencial da variável especificada (10).  
  
```sql
SELECT EXP(10) AS exp  
```  
  
 Aqui está o conjunto de resultados.  
  
```json
[{exp: 22026.465794806718}]  
```  
  
 O exemplo seguinte devolve o valor exponencial do logarithm natural de 20 e o logarithm natural do exponencial de 20. Como estas funções são funções inversas umas das outras, o valor de retorno com arredondamento para matemática de ponto flutuante em ambos os casos é de 20.  
  
```sql
SELECT EXP(LOG(20)) AS exp1, LOG(EXP(20)) AS exp2  
```  
  
 Aqui está o conjunto de resultados.  
  
```json
[{exp1: 19.999999999999996, exp2: 20}]  
```  

## <a name="next-steps"></a>Passos seguintes

- [Funções matemáticas Azure Cosmos DB](sql-query-mathematical-functions.md)
- [Funcionamento do sistema Azure Cosmos DB](sql-query-system-functions.md)
- [Introdução ao Azure Cosmos DB](introduction.md)
