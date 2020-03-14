---
title: ST_DISTANCE na linguagem de consulta do Azure Cosmos DB
description: Saiba mais sobre a função do sistema SQL ST_DISTANCE em Azure Cosmos DB.
author: ginamr
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 09/13/2019
ms.author: girobins
ms.custom: query-reference
ms.openlocfilehash: 972712d37c146ce288c49af7832919946f5503cd
ms.sourcegitcommit: c29b7870f1d478cec6ada67afa0233d483db1181
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/13/2020
ms.locfileid: "79297123"
---
# <a name="st_distance-azure-cosmos-db"></a>ST_DISTANCE (Azure Cosmos DB)
 Devolve a distância entre as duas expressões GeoJSON Point, Polygon, MultiPolygon ou LineString. Para saber mais, consulte o artigo de dados de [localização Geospatial e GeoJSON.](sql-query-geospatial-intro.md)
  
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
  
  O exemplo que se segue mostra como devolver todos os documentos familiares que estão a menos de 30 km da localização especificada usando a função `ST_DISTANCE` incorporada. .  
  
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

Esta função do sistema beneficiará de um [índice geoespacial.](index-policy.md#spatial-indexes)

## <a name="next-steps"></a>Passos Seguintes

- [Funções espaciais Azure Cosmos DB](sql-query-spatial-functions.md)
- [Funcionamento do sistema Azure Cosmos DB](sql-query-system-functions.md)
- [Introdução ao Azure Cosmos DB](introduction.md)
