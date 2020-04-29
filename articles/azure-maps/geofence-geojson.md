---
title: Formato de dados GeoJSON para geovetes Microsoft Azure Maps
description: Neste artigo, você aprenderá sobre como preparar os dados geovetes que podem ser usados no Microsoft Azure Maps GET e POST Geofence API.
author: philmea
ms.author: philmea
ms.date: 02/14/2019
ms.topic: conceptual
ms.service: azure-maps
services: azure-maps
manager: ''
ms.openlocfilehash: 7b9860908dd3bdf3dcda727f350578a97b890cac
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/28/2020
ms.locfileid: "80335611"
---
# <a name="geofencing-geojson-data"></a>Geofencing GeoJSON

Os Mapas Azure [GET Geofence](/rest/api/maps/spatial/getgeofence) e [POST Geofence](/rest/api/maps/spatial/postgeofence) APIs permitem-lhe recuperar a proximidade de uma coordenada relativa a uma geoveda ou conjunto de cercas fornecidas. Este artigo detalha como preparar os dados geovetes que podem ser usados no Azure Maps GET e POST API.

Os dados relativos a geovetes ou `Feature` conjunto `FeatureCollection` de `GeoJSON` geovedações são representados por Object e Object em formato, que é definido em [rfc7946](https://tools.ietf.org/html/rfc7946). Além disso:

* O tipo de objeto GeoJSON pode ser um `Feature` Objeto ou um `FeatureCollection` Objeto.
* O tipo de Objeto `Point`de `MultiPoint` `LineString`Geometria pode ser um, `MultiLineString`, `Polygon`, , `MultiPolygon`e `GeometryCollection`.
* Todas as propriedades `geometryId`da funcionalidade devem conter um , que é usado para identificar a geovedação.
* A `Point`funcionalidade `MultiPoint` `LineString`com, , deve `MultiLineString` conter-se `radius` nas propriedades. `radius`o valor é medido `radius` em metros, o valor varia de 1 a 10000.
* A `polygon` funcionalidade `multipolygon` com e o tipo de geometria não tem uma propriedade de raio.
* `validityTime`é uma propriedade opcional que permite ao utilizador definir prazo expirado e prazo de validade para os dados de geovedação. Se não for especificado, os dados nunca expiram e são sempre válidos.
* Esta `expiredTime` é a data de validade e a hora dos dados geofencing. Se o `userTime` valor do pedido for posterior ao deste valor, os dados geovetes correspondentes são considerados como dados caducados e não são consultados. Após o qual, a geometria destes dados `expiredGeofenceGeometryId` geovetes será incluída na matriz dentro da resposta geoveda.
* É `validityPeriod` uma lista de prazo de validade da geoveda. Se o `userTime` valor do pedido for fora do período de validade, os dados geovetes correspondentes são considerados inválidos e não serão consultados. A geometria destes dados geovetes está incluída na `invalidPeriodGeofenceGeometryId` matriz dentro da resposta geoveda. O quadro seguinte mostra as propriedades do elemento período de validade.

| Nome | Tipo | Necessário  | Descrição |
| :------------ |:------------: |:---------------:| :-----|
| startTime | Datetime  | true | A data de início do período de validade. |
| endTime   | Datetime  | true |  A data limite do período de validade. |
| recorrênciaType | string | false |   O tipo de recorrência do período. O valor `Daily`pode `Weekly` `Monthly`ser, `Yearly`ou . O valor `Daily`predefinido é .|
| businessDayOnly | Booleano | false |  Indique se os dados só são válidos durante os dias úteis. O valor `false`predefinido é .|


* Todos os valores de coordenadasão representados como `WGS84`[longitude, latitude] definidos em .
* Para cada Funcionalidade, `MultiPoint` `MultiLineString`que `MultiPolygon` contém, ou `GeometryCollection`, as propriedades são aplicadas a todos os elementos. por exemplo: Todos `MultiPoint` os pontos em utilizarão o mesmo raio para formar uma geocerca de círculo múltiplo.
* No cenário de círculo de pontos, uma `Point` geometria do círculo pode ser representada usando um objeto de geometria com propriedades elaboradas nas [geometrias GeoJSON de Extensão](https://docs.microsoft.com/azure/azure-maps/extend-geojson).      

Segue-se um corpo de pedido de amostra para uma `GeoJSON` geoveda representada como uma geometria geoveda de círculo na utilização de um ponto central e um raio. O período válido dos dados geovetes começa de 2018-10-22, das 9h às 17h, repetido todos os dias, exceto para o fim de semana. `expiredTime`indica que estes dados de geovedação serão considerados caducados, se `userTime` no pedido for posterior mente inferior a `2019-01-01`.  

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
