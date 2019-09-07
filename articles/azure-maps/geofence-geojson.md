---
title: Formato de dados geojson de limite geográfico no Azure Maps | Microsoft Docs
description: Saiba mais sobre o formato de dados geojson de cerca geográfica no Azure Maps
author: walsehgal
ms.author: v-musehg
ms.date: 02/14/2019
ms.topic: conceptual
ms.service: azure-maps
services: azure-maps
manager: ''
ms.openlocfilehash: 5946180c161a38a30f44e235ce0b626fd70a5400
ms.sourcegitcommit: 97605f3e7ff9b6f74e81f327edd19aefe79135d2
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 09/06/2019
ms.locfileid: "70735132"
---
# <a name="geofencing-geojson-data"></a>Dados geojson de isolamento geográfico

As APIs de [obtenção de cerca geográfica](/rest/api/maps/spatial/getgeofence) e [postagem geográfica](/rest/api/maps/spatial/postgeofence) do Azure Maps permitem que você recupere a proximidade de uma coordenada em relação a uma cerca geográfica fornecida ou a um conjunto de limites. Este artigo fornece detalhes sobre como preparar os dados de limite geográfico que podem ser usados na API GET e POST do Azure Maps.

Os dados para cerca geográfica ou conjunto de limites geográficos são `Feature` representados por `FeatureCollection` objeto e `GeoJSON` objeto no formato, que é definido em [rfc7946](https://tools.ietf.org/html/rfc7946). Além disso:

* O tipo de objeto geojson pode ser `Feature` um objeto ou `FeatureCollection` um objeto.
* O tipo de objeto Geometry pode ser `Point`um `MultiPoint`, `LineString`, `MultiLineString`, `Polygon`, `MultiPolygon`, e `GeometryCollection`.
* Todas as propriedades de recurso devem `geometryId`conter um, que é usado para identificar a cerca geográfica.
* Recurso com `Point`, `MultiPoint`, `LineString`, deve`MultiLineString` conter`radius` em Propriedades. `radius`o valor é medido em metros `radius` , o valor varia de 1 a 10000.
* O recurso `polygon` com `multipolygon` e o tipo geometry não têm uma propriedade RADIUS.
* `validityTime`é uma propriedade opcional que permite que o usuário defina a hora expirada e o período de tempo de validade para os dados de limite geográfico. Se não for especificado, os dados nunca expirarão e serão sempre válidos.
* O `expiredTime` é a data e a hora de expiração dos dados de isolamento geográfica. Se o valor de `userTime` na solicitação for posterior a esse valor, os dados de cerca geográfica correspondentes serão considerados como dados expirados e não serão consultados. Após o qual, a geometryid desses dados de cerca geográfica será incluída na `expiredGeofenceGeometryId` matriz dentro da resposta de cerca geográfica.
* O `validityPeriod` é uma lista de períodos de tempo de validade da cerca geográfica. Se o valor de `userTime` na solicitação cair fora do período de validade, os dados de cerca geográfica correspondentes serão considerados inválidos e não serão consultados. A geometryid desses dados de cerca geográfica está incluída na `invalidPeriodGeofenceGeometryId` matriz dentro da resposta de cerca geográfica. A tabela a seguir mostra as propriedades do elemento validityPeriod.

| Name | Type | Necessário  | Descrição |
| :------------ |:------------: |:---------------:| :-----|
| startTime | Datetime  | true | A data e a hora de início do período de validade. |
| endTime   | Datetime  | true |  A data e a hora de término do período de validade. |
| recurrenceType | Cadeia de caracteres | false |   O tipo de recorrência do período. O valor pode ser `Daily`, `Weekly`, `Monthly`ou `Yearly`. O valor padrão `Daily`é.|
| businessDayOnly | Booleano | false |  Indique se os dados são válidos somente durante os dias úteis. O valor padrão `false`é.|


* Todos os valores de coordenadas são representados como [longitude, latitude] `WGS84`definido em.
* Para cada recurso, que contém `MultiPoint` `MultiPolygon` , `MultiLineString`, ou `GeometryCollection`, as propriedades são aplicadas a todos os elementos. por exemplo: Todos os pontos em `MultiPoint` usarão o mesmo raio para formar uma cerca geográfica de círculo múltiplo.
* No cenário de círculo de ponto, uma geometria de círculo pode ser representada `Point` usando um objeto Geometry com propriedades elaboradas na [extensão de geometrias geojson](https://docs.microsoft.com/azure/azure-maps/extend-geojson).      

Veja a seguir um corpo de solicitação de exemplo para uma cerca geográfica representada como um círculo com `GeoJSON` cerca de cerca de uma geometria no uso de um ponto central e um raio. O período válido dos dados de limite geográfico começa de 2018-10-22, 9:00 às 17:00, repetido todos os dias, exceto o fim de semana. `expiredTime`indica que `userTime` `2019-01-01`os dados de cerca geográfica serão considerados expirados, se na solicitação for posterior a.  

```json
{
    "type": "Feature",
    "geometry": {
        "type": "Point",
        "coordinates": [-122.126986, 47.639754]
    },
    "properties": {
        "geometryId" : "1",
        "subType": "Circle",
        "radius": 500,
        "validityTime": 
        {
            "expiredTime": "2019-01-01T00:00:00",
            "validityPeriod": [
                {
                    "startTime": "2018-10-22T09:00:00",
                    "endTime": "2018-10-22T17:00:00",
                    "recurrenceType": "Daily",
                    "recurrenceFrequency": 1,
                    "businessDayOnly": true
                }
            ]
        }
    }
}
```
