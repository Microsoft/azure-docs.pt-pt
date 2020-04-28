---
title: LOG em linguagem de consulta de Azure Cosmos DB
description: Conheça a função do sistema LOG SQL em Azure Cosmos DB para devolver o logarithm natural da expressão numérica especificada
author: ginamr
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 09/13/2019
ms.author: girobins
ms.custom: query-reference
ms.openlocfilehash: 9213ef03f383dec7109652246411fac154b4a7f9
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/28/2020
ms.locfileid: "78302513"
---
# <a name="log-azure-cosmos-db"></a>LOG (Azure Cosmos DB)
 Devolve o logarithm natural da expressão numérica especificada.  
  
## <a name="syntax"></a>Sintaxe
  
```sql
LOG (<numeric_expr> [, <base>])  
```  
  
## <a name="arguments"></a>Argumentos
  
*numeric_expr*  
   É uma expressão numérica.  
  
*base*  
   Argumento numérico opcional que define a base para o logarithm.  
  
## <a name="return-types"></a>Tipos de retorno
  
  Devolve uma expressão numérica.  
  
## <a name="remarks"></a>Observações
  
  Por predefinição, log() devolve o logarithm natural. Pode alterar a base do logarithm para outro valor utilizando o parâmetro base opcional.  
  
  O logarithm natural é o logarithm para a base **e**, onde **e** é uma constante irracional aproximadamente igual a 2.718281828.  
  
  O logarithm natural do exponencial de um número é o próprio número: LOG( n ) = n. E o exponencial do logarithm natural de um número é o próprio número: EXP( n) = n.  
  
## <a name="examples"></a>Exemplos
  
  O exemplo seguinte declara uma variável e devolve o valor logarithm da variável especificada (10).  
  
```sql
SELECT LOG(10) AS log  
```  
  
 Aqui está o conjunto de resultados.  
  
```json
[{log: 2.3025850929940459}]  
```  
  
 O exemplo seguinte `LOG` calcula o expoente de um número.  
  
```sql
SELECT EXP(LOG(10)) AS expLog  
```  
  
 Aqui está o conjunto de resultados.  
  
```json
[{expLog: 10.000000000000002}]  
```  

## <a name="remarks"></a>Observações

Esta função do sistema não utilizará o índice.

## <a name="next-steps"></a>Passos seguintes

- [Funções matemáticas Azure Cosmos DB](sql-query-mathematical-functions.md)
- [Funcionamento do sistema Azure Cosmos DB](sql-query-system-functions.md)
- [Introdução ao Azure Cosmos DB](introduction.md)
