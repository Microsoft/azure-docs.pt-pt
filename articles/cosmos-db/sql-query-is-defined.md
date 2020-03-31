---
title: IS_DEFINED na linguagem de consulta do Azure Cosmos DB
description: Saiba mais sobre a função do sistema SQL IS_DEFINED em Azure Cosmos DB.
author: ginamr
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 09/13/2019
ms.author: girobins
ms.custom: query-reference
ms.openlocfilehash: a5fcaf52d8e9e6b942a95f0b0c43f3f654c5d5d2
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/28/2020
ms.locfileid: "78303856"
---
# <a name="is_defined-azure-cosmos-db"></a>IS_DEFINED (Azure Cosmos DB)
 Devolve uma Boolean indicando se a propriedade foi atribuída um valor.  
  
## <a name="syntax"></a>Sintaxe
  
```sql
IS_DEFINED(<expr>)  
```  
  
## <a name="arguments"></a>Argumentos
  
*expr*  
   É qualquer expressão.  
  
## <a name="return-types"></a>Tipos de retorno
  
  Devolve uma expressão booleana.  
  
## <a name="examples"></a>Exemplos
  
  Verifica-se o seguinte exemplo da presença de um imóvel dentro do documento Específico JSON. O primeiro regressa verdadeiro desde que "A" está presente, mas o segundo retorna falso uma vez que "b" está ausente.  
  
```sql
SELECT IS_DEFINED({ "a" : 5 }.a) AS isDefined1, IS_DEFINED({ "a" : 5 }.b) AS isDefined2 
```  
  
 Aqui está o conjunto de resultados.  
  
```json
[{"isDefined1":true,"isDefined2":false}]  
```  

## <a name="remarks"></a>Observações

Esta função do sistema beneficiará de um índice de [alcance](index-policy.md#includeexclude-strategy).

## <a name="next-steps"></a>Passos seguintes

- [Funções de verificação de tipos Azure Cosmos DB](sql-query-type-checking-functions.md)
- [Funcionamento do sistema Azure Cosmos DB](sql-query-system-functions.md)
- [Introdução ao Azure Cosmos DB](introduction.md)
