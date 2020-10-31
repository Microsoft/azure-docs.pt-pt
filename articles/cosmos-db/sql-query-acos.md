---
title: ACOS em Azure Cosmos DB linguagem de consulta
description: Saiba como o sistema ACOS (arccosice) SQL funciona em Azure Cosmos DB devolve o ângulo, em radiais, cujo cosine é a expressão numérica especificada
author: ginamr
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 03/03/2020
ms.author: girobins
ms.custom: query-reference
ms.openlocfilehash: 99730b4c67adc841a33ea9d60440b00a19f90e48
ms.sourcegitcommit: 3bdeb546890a740384a8ef383cf915e84bd7e91e
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/30/2020
ms.locfileid: "93090958"
---
# <a name="acos-azure-cosmos-db"></a>ACOS (Azure Cosmos DB)
[!INCLUDE[appliesto-sql-api](includes/appliesto-sql-api.md)]

 Devolve o ângulo, em radiais, cujo cosine é a expressão numérica especificada; também chamado arccosine.  
  
## <a name="syntax"></a>Sintaxe
  
```sql
ACOS(<numeric_expr>)  
```  
  
## <a name="arguments"></a>Argumentos
  
*numeric_expr*  
   É uma expressão numérica.  
  
## <a name="return-types"></a>Tipos de retorno
  
  Devolve uma expressão numérica.  
  
## <a name="examples"></a>Exemplos
  
  O exemplo a seguir devolve o `ACOS` de -1.  
  
```sql
SELECT ACOS(-1) AS acos 
```  
  
 Aqui está o conjunto de resultados.  
  
```json
[{"acos": 3.1415926535897931}]  
```  

## <a name="remarks"></a>Observações

Esta função do sistema não utilizará o índice.

## <a name="next-steps"></a>Passos seguintes

- [Funções matemáticas Azure Cosmos DB](sql-query-mathematical-functions.md)
- [Funciona O sistema Azure Cosmos DB](sql-query-system-functions.md)
- [Introdução ao Azure Cosmos DB](introduction.md)
