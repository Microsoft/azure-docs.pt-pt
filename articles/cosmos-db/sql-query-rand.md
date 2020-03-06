---
title: RAND em linguagem de consulta de Azure Cosmos DB
description: Conheça a função rand do sistema SQL em Azure Cosmos DB.
author: ginamr
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 09/16/2019
ms.author: girobins
ms.custom: query-reference
ms.openlocfilehash: e44878b6d65725f08aeca4eb07088315ae2bb78a
ms.sourcegitcommit: f915d8b43a3cefe532062ca7d7dbbf569d2583d8
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/05/2020
ms.locfileid: "78302224"
---
# <a name="rand-azure-cosmos-db"></a>RAND (Azure Cosmos DB)
 Devolve um valor numérico gerado aleatoriamente a partir de [0,1).
 
## <a name="syntax"></a>Sintaxe
  
```sql
RAND ()  
```  

## <a name="return-types"></a>Tipos de retorno

  Devolve uma expressão numérica.

## <a name="remarks"></a>Observações

  `RAND` é uma função não determinística. Chamadas repetitivas de `RAND` não devolvem os mesmos resultados.

## <a name="examples"></a>Exemplos
  
  O exemplo seguinte devolve um valor numérico gerado aleatoriamente.
  
```sql
SELECT RAND() AS rand 
```  
  
 Aqui está o conjunto de resultados.  
  
```json
[{"rand": 0.87860053195618093}]  
``` 

## <a name="remarks"></a>Observações

Esta função do sistema não utilizará o índice.

## <a name="next-steps"></a>Passos seguintes

- [Funções matemáticas Azure Cosmos DB](sql-query-mathematical-functions.md)
- [Funcionamento do sistema Azure Cosmos DB](sql-query-system-functions.md)
- [Introdução ao Azure Cosmos DB](introduction.md)
