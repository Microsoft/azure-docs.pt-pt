---
title: ATN2 em linguagem de consulta do Azure Cosmos DB
description: Saiba como funciona o sistema ATN2 SQL em Azure Cosmos DB devolve o valor principal do arco tangente de y/x, expresso em radianos
author: ginamr
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 03/03/2020
ms.author: girobins
ms.custom: query-reference
ms.openlocfilehash: 696e14e75998ead04c99fab2b84fc4c742d5f54a
ms.sourcegitcommit: f915d8b43a3cefe532062ca7d7dbbf569d2583d8
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/05/2020
ms.locfileid: "78302666"
---
# <a name="atn2-azure-cosmos-db"></a>ATN2 (Azure Cosmos DB)
 Devolve o valor principal do arco tangente de y / x, expressado em radianos.  
  
## <a name="syntax"></a>Sintaxe
  
```sql
ATN2(<numeric_expr>, <numeric_expr>)  
```  
  
## <a name="arguments"></a>Argumentos
  
*numeric_expr*  
   É uma expressão numérica.  
  
## <a name="return-types"></a>Tipos de retorno
  
  Devolve uma expressão numérica.  
  
## <a name="examples"></a>Exemplos
  
  O exemplo seguinte calcula ATN2 especificado para x e y componentes.  
  
```sql
SELECT ATN2(35.175643, 129.44) AS atn2  
```  
  
 Aqui está o conjunto de resultados.  
  
```json
[{"atn2": 1.3054517947300646}]  
```  

## <a name="remarks"></a>Observações

Esta função do sistema não utilizará o índice.

## <a name="next-steps"></a>Passos seguintes

- [Funções matemáticas Azure Cosmos DB](sql-query-mathematical-functions.md)
- [Funcionamento do sistema Azure Cosmos DB](sql-query-system-functions.md)
- [Introdução ao Azure Cosmos DB](introduction.md)
