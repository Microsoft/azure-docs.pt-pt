---
title: Fazer logon Azure Cosmos DB linguagem de consulta
description: Saiba mais sobre a função de sistema SQL de LOG em Azure Cosmos DB para retornar o logaritmo natural da expressão numérica especificada
author: ginamr
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 09/13/2019
ms.author: girobins
ms.custom: query-reference
ms.openlocfilehash: ae7812670da836efa326b9224547e4d1b64374c2
ms.sourcegitcommit: 9405aad7e39efbd8fef6d0a3c8988c6bf8de94eb
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 12/05/2019
ms.locfileid: "74873289"
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
  
*polybase*  
   Argumento numérico opcional que define a base para o logaritmo.  
  
## <a name="return-types"></a>Tipos de retorno
  
  Devolve uma expressão numérica.  
  
## <a name="remarks"></a>Observações
  
  Por predefinição, o LOG() devolve o logaritmo natural. Pode alterar a base do logaritmo a outro valor utilizando o parâmetro de base opcional.  
  
  O logaritmo natural é o logaritmo para a base **i**, onde **i** é uma constante irracional aproximadamente igual a 2.718281828.  
  
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
  
 O exemplo a seguir calcula o `LOG` para o expoente de um número.  
  
```sql
SELECT EXP(LOG(10)) AS expLog  
```  
  
 Aqui está o conjunto de resultados.  
  
```json
[{expLog: 10.000000000000002}]  
```  

## <a name="next-steps"></a>Passos seguintes

- [Funções matemáticas Azure Cosmos DB](sql-query-mathematical-functions.md)
- [Funções do sistema Azure Cosmos DB](sql-query-system-functions.md)
- [Introdução ao Azure Cosmos DB](introduction.md)
