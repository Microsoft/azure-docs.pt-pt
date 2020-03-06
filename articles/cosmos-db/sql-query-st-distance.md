---
title: ST_DISTANCE na linguagem de consulta do Azure Cosmos DB
description: Saiba mais sobre a função do sistema SQL ST_DISTANCE em Azure Cosmos DB.
author: ginamr
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 09/13/2019
ms.author: girobins
ms.custom: query-reference
ms.openlocfilehash: 4908d5f9f6eccaaaf71308b868d712f0eb96cb52
ms.sourcegitcommit: f915d8b43a3cefe532062ca7d7dbbf569d2583d8
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/05/2020
ms.locfileid: "78303159"
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

## <a name="next-steps"></a>Passos seguintes

- [Funções espaciais Azure Cosmos DB](sql-query-spatial-functions.md)
- [Funcionamento do sistema Azure Cosmos DB](sql-query-system-functions.md)
- [Introdução ao Azure Cosmos DB](introduction.md)
