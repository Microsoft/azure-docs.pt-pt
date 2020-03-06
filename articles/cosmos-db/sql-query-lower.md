---
title: LOWER em linguagem de consulta de Azure Cosmos DB
description: Saiba mais sobre a função do sistema Lower SQL em Azure Cosmos DB para devolver uma expressão de cadeia após converter dados de caracteres maiúsculos em minúsculas
author: ginamr
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 09/13/2019
ms.author: girobins
ms.custom: query-reference
ms.openlocfilehash: 80dba57d4fe05630eb5ae4f8fc96bd0aa214c6d4
ms.sourcegitcommit: f915d8b43a3cefe532062ca7d7dbbf569d2583d8
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/05/2020
ms.locfileid: "78302275"
---
# <a name="lower-azure-cosmos-db"></a>LOWER (Azure Cosmos DB)
 Devolve uma expressão de cadeia de caracteres após a conversão de dados de caráter em maiúsculas em minúsculas.  

A função do sistema LOWER não utiliza o índice. Se planeia fazer comparações frequentes de casos insensíveis, a função do sistema LOWER pode consumir uma quantidade significativa de RU's. Se for esse o caso, em vez de utilizar a função do sistema LOWER para normalizar os dados de cada vez para comparações, pode normalizar o invólucro após a inserção. Em seguida, uma consulta como SELECT * From c WHERE LOWER (c.name) = 'bob' torna-se simplesmente SELECT * DE c ONDE c.name = 'bob'.

## <a name="syntax"></a>Sintaxe
  
```sql
LOWER(<str_expr>)  
```  
  
## <a name="arguments"></a>Argumentos
  
*str_expr*  
   É uma expressão de cordas.  
  
## <a name="return-types"></a>Tipos de retorno
  
  Devolve uma expressão de cadeia.  
  
## <a name="examples"></a>Exemplos
  
  O exemplo que se segue mostra como usar `LOWER` numa consulta.  
  
```sql
SELECT LOWER("Abc") AS lower
```  
  
 Aqui está o conjunto de resultados.  
  
```json
[{"lower": "abc"}]  
  
```  

## <a name="remarks"></a>Observações

Esta função do sistema não utilizará o índice.

## <a name="next-steps"></a>Passos seguintes

- [Funções de corda Azure Cosmos DB](sql-query-string-functions.md)
- [Funcionamento do sistema Azure Cosmos DB](sql-query-system-functions.md)
- [Introdução ao Azure Cosmos DB](introduction.md)
