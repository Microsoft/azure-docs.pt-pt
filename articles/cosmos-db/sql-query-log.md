---
title: LOG em Azure Cosmos DB linguagem de consulta
description: Conheça a função do sistema LOG SQL em Azure Cosmos DB para devolver o logaritmo natural da expressão numérica especificada
author: ginamr
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 09/13/2019
ms.author: girobins
ms.custom: query-reference
ms.openlocfilehash: 74d07076fb322214348d52fe65dbc98bdff3cd44
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/09/2020
ms.locfileid: "88798266"
---
# <a name="log-azure-cosmos-db"></a>LOG (Azure Cosmos DB)
 Devolve o logaritmo natural da expressão numérica especificada.  
  
## <a name="syntax"></a>Sintaxe
  
```sql
LOG (<numeric_expr> [, <base>])  
```  
  
## <a name="arguments"></a>Argumentos
  
*numeric_expr*  
   É uma expressão numérica.  
  
*base*  
   Argumento numérico opcional que define a base para o logaritmo.  
  
## <a name="return-types"></a>Tipos de retorno
  
  Devolve uma expressão numérica.  
  
## <a name="remarks"></a>Observações
  
  Por predefinição, LOG() devolve o logaritmo natural. Pode alterar a base do logaritmo para outro valor utilizando o parâmetro base opcional.  
  
  O logaritmo natural é o logaritmo da base **e,** onde **e** é uma constante irracional aproximadamente igual a 2.718281828.  
  
  O logaritmo natural do exponencial de um número é o número em si: LOG(n) = n. E o exponencial do logaritmo natural de um número é o número em si: EXP(n) = n.

  Esta função do sistema não utilizará o índice.
  
## <a name="examples"></a>Exemplos
  
  O exemplo a seguir declara uma variável e devolve o valor logaritmo da variável especificada (10).  
  
```sql
SELECT LOG(10) AS log  
```  
  
 Aqui está o conjunto de resultados.  
  
```json
[{log: 2.3025850929940459}]  
```  
  
 O exemplo a seguir calcula o `LOG` expoente de um número.  
  
```sql
SELECT EXP(LOG(10)) AS expLog  
```  
  
 Aqui está o conjunto de resultados.  
  
```json
[{expLog: 10.000000000000002}]  
```  

## <a name="next-steps"></a>Passos seguintes

- [Funções matemáticas Azure Cosmos DB](sql-query-mathematical-functions.md)
- [Funciona O sistema Azure Cosmos DB](sql-query-system-functions.md)
- [Introdução ao Azure Cosmos DB](introduction.md)
