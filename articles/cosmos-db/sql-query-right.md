---
title: Direito na linguagem de consulta do Azure Cosmos DB
description: Saiba sobre a função do sistema SQL EM Azure Cosmos DB.
author: ginamr
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 03/03/2020
ms.author: girobins
ms.custom: query-reference
ms.openlocfilehash: 247616d2ac4f2a5799a5896d679f6e6d5917d5a6
ms.sourcegitcommit: f915d8b43a3cefe532062ca7d7dbbf569d2583d8
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/05/2020
ms.locfileid: "78302156"
---
# <a name="right-azure-cosmos-db"></a>DIREITO (Azure Cosmos DB)
 Devolve a parte direita de uma cadeia de caracteres com o número especificado de carateres.  
  
## <a name="syntax"></a>Sintaxe
  
```sql
RIGHT(<str_expr>, <num_expr>)  
```  
  
## <a name="arguments"></a>Argumentos
  
*str_expr*  
   É a expressão de corda para extrair personagens de.  
  
*num_expr*  
   É uma expressão numérica que especifica o número de caracteres.  
  
## <a name="return-types"></a>Tipos de retorno
  
  Devolve uma expressão de cadeia.  
  
## <a name="examples"></a>Exemplos
  
  O exemplo seguinte devolve a parte direita de "abc" para vários valores de comprimento.  
  
```sql
SELECT RIGHT("abc", 1) AS r1, RIGHT("abc", 2) AS r2 
```  
  
 Aqui está o conjunto de resultados.  
  
```json
[{"r1": "c", "r2": "bc"}]  
```  

## <a name="remarks"></a>Observações

Esta função do sistema não utilizará o índice.

## <a name="next-steps"></a>Passos seguintes

- [Funções de corda Azure Cosmos DB](sql-query-string-functions.md)
- [Funcionamento do sistema Azure Cosmos DB](sql-query-system-functions.md)
- [Introdução ao Azure Cosmos DB](introduction.md)
