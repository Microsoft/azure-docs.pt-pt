---
title: EXP na linguagem de consulta Azure Cosmos DB
description: Saiba mais sobre a função de sistema SQL do expoente (EXP) no Azure Cosmos DB para retornar o valor exponencial da expressão numérica especificada
author: ginamr
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 09/13/2019
ms.author: girobins
ms.custom: query-reference
ms.openlocfilehash: 76d614264124e1ce4138663b702ff6d899b3aa4e
ms.sourcegitcommit: 9405aad7e39efbd8fef6d0a3c8988c6bf8de94eb
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 12/05/2019
ms.locfileid: "74873323"
---
# <a name="exp-azure-cosmos-db"></a>EXP (Azure Cosmos DB)
 Devolve o valor exponencial da expressão especificada numérico.  
  
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
  
  A constante **i** (2.718281...), é a base do logaritmos naturais.  
  
  O expoente de um número é a constante **i** elevado à potência do número. Por exemplo, EXP(1.0) = e ^ 1.0 = 2.71828182845905 e EXP(10) = e ^ 10 = 22026.4657948067.  
  
  Exponencial do logaritmo natural de um número é o número em si: EXP (LOG (n)) = n. E o logaritmo natural de exponencial de um número é o número em si: registo (EXP (n)) = n.  
  
## <a name="examples"></a>Exemplos
  
  O exemplo seguinte declara uma variável e devolve o valor exponencial da variável especificada (10).  
  
```sql
SELECT EXP(10) AS exp  
```  
  
 Aqui está o conjunto de resultados.  
  
```json
[{exp: 22026.465794806718}]  
```  
  
 O exemplo seguinte devolve o valor exponencial de natural logarithm de 20 e o logaritmo natural de exponencial de 20. Uma vez que estas funções são funções inversas entre si, o valor de retorno com o arredondamento para matemática de ponto flutuante em ambos os casos é 20.  
  
```sql
SELECT EXP(LOG(20)) AS exp1, LOG(EXP(20)) AS exp2  
```  
  
 Aqui está o conjunto de resultados.  
  
```json
[{exp1: 19.999999999999996, exp2: 20}]  
```  

## <a name="next-steps"></a>Passos seguintes

- [Funções matemáticas Azure Cosmos DB](sql-query-mathematical-functions.md)
- [Funções do sistema Azure Cosmos DB](sql-query-system-functions.md)
- [Introdução ao Azure Cosmos DB](introduction.md)
