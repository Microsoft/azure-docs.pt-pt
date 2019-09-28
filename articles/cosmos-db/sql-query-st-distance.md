---
title: ST_DISTANCE na linguagem de consulta Azure Cosmos DB
description: Saiba mais sobre a função do sistema SQL ST_DISTANCE no Azure Cosmos DB.
author: ginamr
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 09/13/2019
ms.author: girobins
ms.custom: query-reference
ms.openlocfilehash: 1c55bac14b3379f29d57bbad36026749089ec0fd
ms.sourcegitcommit: 7f6d986a60eff2c170172bd8bcb834302bb41f71
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 09/27/2019
ms.locfileid: "71349407"
---
# <a name="st_distance-azure-cosmos-db"></a>ST_DISTANCE (Azure Cosmos DB)
 Devolve a distância entre as duas expressões LineString, Polygon ou GeoJSON ponto.  
  
## <a name="syntax"></a>Sintaxe
  
```sql
ST_DISTANCE (<spatial_expr>, <spatial_expr>)  
```  
  
## <a name="arguments"></a>Argumentos
  
*spatial_expr*  
   É qualquer expressão de objeto GeoJSON ponto, Polygon ou LineString válida.  
  
## <a name="return-types"></a>Tipos de retorno
  
  Devolve uma expressão numérica, que contém a distância. Este valor é expresso em metros para o sistema de referência do padrão.  
  
## <a name="examples"></a>Exemplos
  
  O exemplo a seguir mostra como retornar todos os documentos de família que estão dentro de 30 km do local especificado usando a função interna `ST_DISTANCE`. .  
  
```sql
SELECT f.id   
FROM Families f   
WHERE ST_DISTANCE(f.location, {'type': 'Point', 'coordinates':[31.9, -4.8]}) < 30000  
```  
  
 Aqui está o conjunto de resultados.  
  
```json
[{  
  "id": "WakefieldFamily"  
}]  
```  

## <a name="next-steps"></a>Passos seguintes

- [Funções espaciais Azure Cosmos DB](sql-query-spatial-functions.md)
- [Funções do sistema Azure Cosmos DB](sql-query-system-functions.md)
- [Introdução ao Azure Cosmos DB](introduction.md)
