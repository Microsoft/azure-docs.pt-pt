---
title: ST_DISTANCE na linguagem de consulta DB do Cosmos Azure
description: Saiba mais sobre a função do sistema SQL ST_DISTANCE em Azure Cosmos DB.
author: ginamr
ms.service: cosmos-db
ms.subservice: cosmosdb-sql
ms.topic: conceptual
ms.date: 02/17/2021
ms.author: girobins
ms.custom: query-reference
ms.openlocfilehash: f01f5faf68821fe9f85657c74111efdbb02bd204
ms.sourcegitcommit: de98cb7b98eaab1b92aa6a378436d9d513494404
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 02/17/2021
ms.locfileid: "100559928"
---
# <a name="st_distance-azure-cosmos-db"></a>ST_DISTANCE (Azure Cosmos DB)
[!INCLUDE[appliesto-sql-api](includes/appliesto-sql-api.md)]

 Devolve a distância entre as duas expressões GeoJSON Point, Polygon, MultiPolygon ou LineString. Para saber mais, consulte o artigo [de localização Geospatial e GeoJSON.](sql-query-geospatial-intro.md)
  
## <a name="syntax"></a>Sintaxe
  
```sql
ST_DISTANCE (<spatial_expr>, <spatial_expr>)  
```  
  
## <a name="arguments"></a>Argumentos
  
*spatial_expr*  
   É qualquer expressão válida de objetos GeoJSON Point, Polygon ou LineString.  
  
## <a name="return-types"></a>Tipos de retorno
  
  Devolve uma expressão numérica que contém a distância. Isto é expresso em metros para o sistema de referência predefinido.  
  
## <a name="examples"></a>Exemplos
  
  O exemplo a seguir mostra como devolver todos os documentos familiares que estão a menos de 30 km do local especificado utilizando a `ST_DISTANCE` função incorporada. .  
  
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

## <a name="remarks"></a>Observações

Esta função do sistema beneficiará de um [índice geoespacial,](index-policy.md#spatial-indexes) exceto em consultas com agregados.

## <a name="next-steps"></a>Passos seguintes

- [Funções espaciais Azure Cosmos DB](sql-query-spatial-functions.md)
- [Funciona O sistema Azure Cosmos DB](sql-query-system-functions.md)
- [Introdução ao Azure Cosmos DB](introduction.md)
