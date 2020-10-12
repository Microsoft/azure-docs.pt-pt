---
title: IS_DEFINED na linguagem de consulta DB do Cosmos Azure
description: Saiba mais sobre a função do sistema SQL IS_DEFINED em Azure Cosmos DB.
author: ginamr
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 09/13/2019
ms.author: girobins
ms.custom: query-reference
ms.openlocfilehash: a5fcaf52d8e9e6b942a95f0b0c43f3f654c5d5d2
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/09/2020
ms.locfileid: "78303856"
---
# <a name="is_defined-azure-cosmos-db"></a>IS_DEFINED (Azure Cosmos DB)
 Devolve um Boolean indicando se a propriedade foi atribuída um valor.  
  
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
  
  O exemplo seguinte verifica a presença de um imóvel dentro do documento JSON especificado. O primeiro retorna verdadeiro uma vez que "a" está presente, mas o segundo é falso uma vez que "b" está ausente.  
  
```sql
SELECT IS_DEFINED({ "a" : 5 }.a) AS isDefined1, IS_DEFINED({ "a" : 5 }.b) AS isDefined2 
```  
  
 Aqui está o conjunto de resultados.  
  
```json
[{"isDefined1":true,"isDefined2":false}]  
```  

## <a name="remarks"></a>Observações

Esta função do sistema beneficiará de um [índice de intervalo.](index-policy.md#includeexclude-strategy)

## <a name="next-steps"></a>Passos seguintes

- [Funções de verificação de tipo Azure Cosmos DB](sql-query-type-checking-functions.md)
- [Funciona O sistema Azure Cosmos DB](sql-query-system-functions.md)
- [Introdução ao Azure Cosmos DB](introduction.md)
