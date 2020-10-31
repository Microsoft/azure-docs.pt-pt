---
title: EXP em Azure Cosmos DB linguagem de consulta
description: Conheça a função do sistema Exponent (EXP) SQL em Azure Cosmos DB para devolver o valor exponencial da expressão numérica especificada
author: ginamr
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 09/13/2019
ms.author: girobins
ms.custom: query-reference
ms.openlocfilehash: d12ab39474f1df139bf2f9064d43f5ab7ecda16b
ms.sourcegitcommit: 3bdeb546890a740384a8ef383cf915e84bd7e91e
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/30/2020
ms.locfileid: "93100274"
---
# <a name="exp-azure-cosmos-db"></a>EXP (Azure Cosmos DB)
[!INCLUDE[appliesto-sql-api](includes/appliesto-sql-api.md)]

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
  
  A constante **e** (2.718281...), é a base dos logaritmos naturais.  
  
  O expoente de um número é a constante **e** elevada ao poder do número. Por exemplo, EXP(1.0) = e^1.0 = 2.71828182845905 e EXP(10) = e^10 = 22026.4657948067.  
  
  O exponencial do logaritmo natural de um número é o número em si: EXP (LOG (n)) = n. E o logaritmo natural do exponencial de um número é o número em si: LOG (EXP (n)) = n.  
  
## <a name="examples"></a>Exemplos
  
  O exemplo a seguir declara uma variável e devolve o valor exponencial da variável especificada (10).  
  
```sql
SELECT EXP(10) AS exp  
```  
  
 Aqui está o conjunto de resultados.  
  
```json
[{exp: 22026.465794806718}]  
```  
  
 O exemplo a seguir devolve o valor exponencial do logaritmo natural de 20 e o logaritmo natural do exponencial de 20. Como estas funções são funções inversas umas das outras, o valor de retorno com arredondamento para matemática de ponto flutuante em ambos os casos é de 20.  
  
```sql
SELECT EXP(LOG(20)) AS exp1, LOG(EXP(20)) AS exp2  
```  
  
 Aqui está o conjunto de resultados.  
  
```json
[{exp1: 19.999999999999996, exp2: 20}]  
```  

## <a name="next-steps"></a>Passos seguintes

- [Funções matemáticas Azure Cosmos DB](sql-query-mathematical-functions.md)
- [Funciona O sistema Azure Cosmos DB](sql-query-system-functions.md)
- [Introdução ao Azure Cosmos DB](introduction.md)
