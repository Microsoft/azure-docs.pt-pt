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
ms.sourcegitcommit: f915d8b43a3cefe532062ca7d7dbbf569d2583d8
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/05/2020
ms.locfileid: "78302513"
---
# <a name="log-azure-cosmos-db"></a>LOG (Azure Cosmos DB)
 Devolve o logaritmo natural da expressão numérica especificado.  
  
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
  
  Por predefinição, o LOG() devolve o logaritmo natural. Pode alterar a base do logaritmo a outro valor utilizando o parâmetro de base opcional.  
  
  O logarithm natural é o logarithm para a base **e**, onde **e** é uma constante irracional aproximadamente igual a 2.718281828.  
  
  O logaritmo natural de exponencial de um número é o número em si: registo (EXP (n)) = n. E exponencial do logaritmo natural de um número é o número em si: EXP (LOG (n)) = n.  
  
## <a name="examples"></a>Exemplos
  
  O exemplo seguinte declara uma variável e devolve o valor de logaritmo da variável especificada (10).  
  
```sql
SELECT LOG(10) AS log  
```  
  
 Aqui está o conjunto de resultados.  
  
```json
[{log: 2.3025850929940459}]  
```  
  
 O exemplo seguinte calcula a `LOG` para o expoente de um número.  
  
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
