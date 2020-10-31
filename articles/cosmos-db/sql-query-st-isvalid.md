---
title: ST_ISVALID na linguagem de consulta DB do Cosmos Azure
description: Saiba mais sobre a função do sistema SQL ST_ISVALID em Azure Cosmos DB.
author: ginamr
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 09/13/2019
ms.author: girobins
ms.custom: query-reference
ms.openlocfilehash: c1af008889ece71bc2158e0a74c30ef17d24207f
ms.sourcegitcommit: 3bdeb546890a740384a8ef383cf915e84bd7e91e
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/30/2020
ms.locfileid: "93080061"
---
# <a name="st_isvalid-azure-cosmos-db"></a>ST_ISVALID (Azure Cosmos DB)
[!INCLUDE[appliesto-sql-api](includes/appliesto-sql-api.md)]

 Devolve um valor Boolean que indica se a expressão GeoJSON, Polygon ou LineString especificada é válida.  
  
## <a name="syntax"></a>Sintaxe
  
```sql
ST_ISVALID(<spatial_expr>)  
```  
  
## <a name="arguments"></a>Argumentos
  
*spatial_expr*  
   É uma expressão GeoJSON Point, Polygon ou LineString.  
  
## <a name="return-types"></a>Tipos de retorno
  
  Devolve uma expressão booleana.  
  
## <a name="examples"></a>Exemplos
  
  O exemplo a seguir mostra como verificar se um ponto é válido usando ST_VALID.  
  
  Por exemplo, este ponto tem um valor de latitude que não está na gama válida de valores [-90, 90], pelo que a consulta retorna falsa.  
  
  Para os polígonos, a especificação GeoJSON requer que o último par de coordenadas fornecido seja o mesmo que o primeiro, para criar uma forma fechada. Os pontos dentro de um polígono devem ser especificados em ordem anti-horário. Um polígono especificado na ordem do relógio representa o inverso da região dentro dela.  
  
```sql
SELECT ST_ISVALID({ "type": "Point", "coordinates": [31.9, -132.8] }) AS b 
```  
  
 Aqui está o conjunto de resultados.  
  
```json
[{ "b": false }]  
```  

## <a name="next-steps"></a>Passos seguintes

- [Funções espaciais Azure Cosmos DB](sql-query-spatial-functions.md)
- [Funciona O sistema Azure Cosmos DB](sql-query-system-functions.md)
- [Introdução ao Azure Cosmos DB](introduction.md)
