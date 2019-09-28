---
title: ST_ISVALIDDETAILED na linguagem de consulta Azure Cosmos DB
description: Saiba mais sobre a função do sistema SQL ST_ISVALIDDETAILED no Azure Cosmos DB.
author: ginamr
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 09/13/2019
ms.author: girobins
ms.custom: query-reference
ms.openlocfilehash: 9e640c223c2fef844b9b53e1f4afa3a5d398c8c0
ms.sourcegitcommit: 7f6d986a60eff2c170172bd8bcb834302bb41f71
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 09/27/2019
ms.locfileid: "71349347"
---
# <a name="st_isvaliddetailed-azure-cosmos-db"></a>ST_ISVALIDDETAILED (Azure Cosmos DB)
 Devolve um valor JSON que contém um valor booleano valor se a expressão de LineString, Polygon ou GeoJSON ponto especificada é válida e se for inválido, além do motivo pelo qual como um valor de cadeia de caracteres.  
  
## <a name="syntax"></a>Sintaxe
  
```sql
ST_ISVALIDDETAILED(<spatial_expr>)  
```  
  
## <a name="arguments"></a>Argumentos
  
*spatial_expr*  
   É uma expressão de ponto ou polígono geojson.  
  
## <a name="return-types"></a>Tipos de retorno
  
  Devolve um valor JSON que contém um valor booleano valor se a expressão de ponto ou de polígono de GeoJSON especificada é válida e se for inválido, além do motivo pelo qual como um valor de cadeia de caracteres.  
  
## <a name="examples"></a>Exemplos
  
  O exemplo a seguir como verificar a validade (com detalhes) usando `ST_ISVALIDDETAILED`.  
  
```sql
SELECT ST_ISVALIDDETAILED({   
  "type": "Polygon",   
  "coordinates": [[ [ 31.8, -5 ], [ 31.8, -4.7 ], [ 32, -4.7 ], [ 32, -5 ] ]]  
}) AS b 
```  
  
 Aqui está o conjunto de resultados.  
  
```json
[{  
  "b": {
    "valid": false,
    "reason": "The Polygon input is not valid because the start and end points of the ring number 1 are not the same. Each ring of a polygon must have the same start and end points."   
  }  
}]  
```  

## <a name="next-steps"></a>Passos seguintes

- [Funções espaciais Azure Cosmos DB](sql-query-spatial-functions.md)
- [Funções do sistema Azure Cosmos DB](sql-query-system-functions.md)
- [Introdução ao Azure Cosmos DB](introduction.md)
