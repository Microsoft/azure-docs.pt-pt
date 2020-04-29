---
title: ST_DISTANCE na linguagem de consulta do Azure Cosmos DB
description: Saiba mais sobre a função do sistema SQL ST_DISTANCE em Azure Cosmos DB.
author: ginamr
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 03/12/2020
ms.author: girobins
ms.custom: query-reference
ms.openlocfilehash: 02844569137a46ea030b2189191b84a9db24ed22
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/28/2020
ms.locfileid: "79537300"
---
# <a name="st_distance-azure-cosmos-db"></a>ST_DISTANCE (Azure Cosmos DB)
 Devolve a distância entre as duas expressões GeoJSON Point, Polygon, MultiPolygon ou LineString. Para saber mais, consulte o artigo de dados de [localização Geospatial e GeoJSON.](sql-query-geospatial-intro.md)
  
## <a name="syntax"></a>Sintaxe
  
```sql
ST_DISTANCE (<spatial_expr>, <spatial_expr>)  
```  
  
## <a name="arguments"></a>Argumentos
  
*spatial_expr*  
   É qualquer expressão válida de objeto GeoJSON Point, Polygon ou LineString.  
  
## <a name="return-types"></a>Tipos de retorno
  
  Devolve uma expressão numérica contendo a distância. Isto é expresso em contadores para o sistema de referência padrão.  
  
## <a name="examples"></a>Exemplos
  
  O exemplo que se segue mostra como devolver todos os documentos `ST_DISTANCE` familiares que estão a menos de 30 km da localização especificada utilizando a função incorporada. .  
  
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

## <a name="next-steps"></a>Passos seguintes

- [Funções espaciais Azure Cosmos DB](sql-query-spatial-functions.md)
- [Funcionamento do sistema Azure Cosmos DB](sql-query-system-functions.md)
- [Introdução ao Azure Cosmos DB](introduction.md)
