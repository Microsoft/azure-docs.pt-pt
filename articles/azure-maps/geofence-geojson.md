---
title: Formato de dados GeoJSON para geofence | Microsoft Azure Maps
description: Saiba mais sobre os dados de geofência do Azure Maps. Veja como utilizar as APIs GET Geofence e POST Geofence ao recuperar a posição das coordenadas em relação a uma geofence.
author: anastasia-ms
ms.author: v-stharr
ms.date: 02/14/2019
ms.topic: conceptual
ms.service: azure-maps
services: azure-maps
manager: ''
ms.openlocfilehash: e880710b93a6764df50780e685c89b5f569b4ec0
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/29/2021
ms.locfileid: "92897199"
---
# <a name="geofencing-geojson-data"></a>Geofencing Dados geojSON

Os Azure Maps [GET Geofence](/rest/api/maps/spatial/getgeofence) e [POST Geofence](/rest/api/maps/spatial/postgeofence) APIs permitem-lhe recuperar a proximidade de uma coordenada relativa a uma geofência fornecida ou conjunto de cercas. Este artigo detalha como preparar os dados de geofência que podem ser usados no Azure Maps GET e POST API.

Os dados relativos à geofência ou conjunto de geofências são representados por `Feature` Object and Object em `FeatureCollection` `GeoJSON` formato, que é definido em [rfc7946](https://tools.ietf.org/html/rfc7946). Além disso:

* O tipo de Objeto GeoJSON pode ser um `Feature` objeto ou um `FeatureCollection` objeto.
* O tipo de Objeto de Geometria pode ser um `Point` , , , , , e `MultiPoint` `LineString` `MultiLineString` `Polygon` `MultiPolygon` `GeometryCollection` .
* Todas as propriedades de recurso devem conter um `geometryId` , que é usado para identificar a geofence.
* Característica com `Point` , , deve conter `MultiPoint` `LineString` `MultiLineString` `radius` propriedades. `radius` valor é medido em metros, o `radius` valor varia de 1 a 10000.
* Recurso com `polygon` e `multipolygon` tipo de geometria não tem uma propriedade de raio.
* `validityTime` é uma propriedade opcional que permite ao utilizador definir o tempo e prazo de validade para os dados de geofência. Se não forem especificados, os dados nunca expiram e são sempre válidos.
* É `expiredTime` a data de validade e a hora dos dados de geofencagem. Se o valor do `userTime` pedido for posterior a este valor, os dados de geofência correspondentes são considerados dados caducados e não são consultados. Sobre o qual a geometriaId destes dados de geofência será incluída na `expiredGeofenceGeometryId` matriz dentro da resposta de geofência.
* `validityPeriod`Trata-se de uma lista de períodos de validade da geo-fence. Se o valor do `userTime` pedido ficar fora do período de validade, os dados de geofência correspondentes são considerados inválidos e não serão consultados. A geometriaId destes dados de geofência está incluída na `invalidPeriodGeofenceGeometryId` matriz dentro da resposta de geofência. A tabela a seguir mostra as propriedades do elemento validadePeriod.

| Nome | Tipo | Necessário  | Descrição |
| :------------ |:------------: |:---------------:| :-----|
| startTime | Datetime  | true | A data de início do período de tempo de validade. |
| endTime   | Datetime  | true |  A data de fim do período de tempo de validade. |
| recorrênciaType | string | false |   O tipo de recorrência do período. O valor pode `Daily` `Weekly` ser, `Monthly` , ou . `Yearly` . O valor predefinido é `Daily`.|
| businessDayOnly | Booleano | false |  Indicar se os dados são válidos apenas durante os dias úteis. O valor predefinido é `false`.|


* Todos os valores de coordenadas são representados como [longitude, latitude] definido em `WGS84` .
* Para cada Recurso, que contém `MultiPoint` , ou , as propriedades são `MultiLineString` `MultiPolygon` `GeometryCollection` aplicadas a todos os elementos. por exemplo: Todos os pontos em `MultiPoint` vão usar o mesmo raio para formar uma geofência de múltiplos círculos.
* Em cenário de círculo pontual, uma geometria do círculo pode ser representada usando um `Point` objeto de geometria com propriedades elaboradas na [extensão das geometrias geoJSON.](./extend-geojson.md)      

Segue-se um corpo de pedido de amostra para uma geofence representada como geometria de geofência de círculo na `GeoJSON` utilização de um ponto central e um raio. O período válido dos dados de geofência começa de 2018-10-22, das 9h às 17h, repetidos todos os dias, exceto ao fim de semana. `expiredTime` indica que estes dados de geofência serão considerados expirados, se `userTime` no pedido for mais tarde do que `2019-01-01` .  

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