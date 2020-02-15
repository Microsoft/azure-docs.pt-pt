---
title: Formato de dados GeoJSON para geovetes Microsoft Azure Maps
description: Neste artigo, você aprenderá sobre como preparar os dados geovetes que podem ser usados no Microsoft Azure Maps GET e POST Geofence API.
author: farah-alyasari
ms.author: v-faalya
ms.date: 02/14/2019
ms.topic: conceptual
ms.service: azure-maps
services: azure-maps
manager: ''
ms.openlocfilehash: 7d1c9a1587771a020f5c9f89e2497a25eb1bba70
ms.sourcegitcommit: 2823677304c10763c21bcb047df90f86339e476a
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 02/14/2020
ms.locfileid: "77210026"
---
# <a name="geofencing-geojson-data"></a>Geofencing GeoJSON

Os Mapas Azure [GET Geofence](/rest/api/maps/spatial/getgeofence) e [POST Geofence](/rest/api/maps/spatial/postgeofence) APIs permitem-lhe recuperar a proximidade de uma coordenada relativa a uma geoveda ou conjunto de cercas fornecidas. Este artigo detalha como preparar os dados geovetes que podem ser usados no Azure Maps GET e POST API.

Os dados relativos a geovetes ou conjunto de geovedações são representados por objeto `Feature` e objeto `FeatureCollection` em formato `GeoJSON`, que é definido em [rfc7946](https://tools.ietf.org/html/rfc7946). Além disso:

* O tipo de objeto GeoJSON pode ser um objeto `Feature` ou um objeto `FeatureCollection`.
* O tipo de Objeto de Geometria pode ser um `Point`, `MultiPoint`, `LineString`, `MultiLineString`, `Polygon`, `MultiPolygon`e `GeometryCollection`.
* Todas as propriedades da funcionalidade devem conter uma `geometryId`, que é usada para identificar a geovedação.
* Recurso com `Point`, `MultiPoint`, `LineString`, `MultiLineString` deve conter `radius` em propriedades. `radius` valor é medido em metros, o valor `radius` varia de 1 a 10000.
* A funcionalidade com `polygon` e `multipolygon` tipo de geometria não tem uma propriedade de raio.
* `validityTime` é uma propriedade opcional que permite ao utilizador definir prazo e tempo de validade expirado para os dados de geovedação. Se não for especificado, os dados nunca expiram e são sempre válidos.
* O `expiredTime` é a data de validade e a hora dos dados geofencing. Se o valor da `userTime` no pedido for posterior a este valor, os dados geovetes correspondentes são considerados como dados caducados e não são consultados. Após o qual, a geometria destes dados geovetes será incluída na `expiredGeofenceGeometryId` matriz dentro da resposta geoveda.
* O `validityPeriod` é uma lista de período de validade da geoveda. Se o valor do `userTime` no pedido for fora do prazo de validade, os dados geovetes correspondentes são considerados inválidos e não serão consultados. A geometria destes dados geovetes está incluída na `invalidPeriodGeofenceGeometryId` matriz dentro da resposta geoveda. O quadro seguinte mostra as propriedades do elemento período de validade.

| Nome | Tipo | Necessário  | Descrição |
| :------------ |:------------: |:---------------:| :-----|
| startTime | Datetime  | true | A data de início do período de validade. |
| endTime   | Datetime  | true |  A data limite do período de validade. |
| recurrenceType | string | false |   O tipo de recorrência do período. O valor pode ser `Daily`, `Weekly`, `Monthly`ou `Yearly`. O valor predefinido é `Daily`.|
| businessDayOnly | Booleano | false |  Indique se os dados só são válidos durante os dias úteis. O valor predefinido é `false`.|


* Todos os valores de coordenadasão representados como [longitude, latitude] definidos em `WGS84`.
* Para cada Recurso, que contém `MultiPoint`, `MultiLineString`, `MultiPolygon` , ou `GeometryCollection`, as propriedades são aplicadas a todos os elementos. por exemplo: Todos os pontos `MultiPoint` usarão o mesmo raio para formar uma geocerca de círculos múltiplos.
* No cenário de círculo de pontos, uma geometria do círculo pode ser representada usando um objeto de geometria `Point` com propriedades elaboradas nas [geometrias GeoJSON de Extensão](https://docs.microsoft.com/azure/azure-maps/extend-geojson).      

Segue-se um corpo de pedido de amostra para uma geoveda representada como uma geometria geoveda de círculo em `GeoJSON` usando um ponto central e um raio. O período válido dos dados geovetes começa de 2018-10-22, das 9h às 17h, repetido todos os dias, exceto para o fim de semana. `expiredTime` indica que estes dados de geovedação serão considerados caducados, se `userTime` no pedido for inferior a `2019-01-01`.  

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
