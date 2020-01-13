---
title: Formato de dados geojson para cerca de cerca | Mapas do Microsoft Azure
description: Neste artigo, você aprenderá a preparar os dados de limite geográfico que podem ser usados no Microsoft Azure mapeia as APIs GET e POST de cerca geográfica.
author: walsehgal
ms.author: v-musehg
ms.date: 02/14/2019
ms.topic: conceptual
ms.service: azure-maps
services: azure-maps
manager: ''
ms.openlocfilehash: f853962bba7302affd78d5ef267460893ea80a33
ms.sourcegitcommit: f9601bbccddfccddb6f577d6febf7b2b12988911
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 01/12/2020
ms.locfileid: "75911582"
---
# <a name="geofencing-geojson-data"></a>Dados geojson de isolamento geográfico

As APIs de [obtenção de cerca geográfica](/rest/api/maps/spatial/getgeofence) e [postagem geográfica](/rest/api/maps/spatial/postgeofence) do Azure Maps permitem que você recupere a proximidade de uma coordenada em relação a uma cerca geográfica fornecida ou a um conjunto de limites. Este artigo fornece detalhes sobre como preparar os dados de limite geográfico que podem ser usados na API GET e POST do Azure Maps.

Os dados para cerca geográfica ou conjunto de limites geográficos são representados por `Feature` objeto e `FeatureCollection` objeto no formato `GeoJSON`, que é definido em [rfc7946](https://tools.ietf.org/html/rfc7946). Além disso:

* O tipo de objeto geojson pode ser um objeto `Feature` ou um objeto `FeatureCollection`.
* O tipo de objeto Geometry pode ser um `Point`, `MultiPoint`, `LineString`, `MultiLineString`, `Polygon`, `MultiPolygon`e `GeometryCollection`.
* Todas as propriedades de recurso devem conter um `geometryId`, que é usado para identificar a cerca geográfica.
* Recurso com `Point`, `MultiPoint`, `LineString``MultiLineString` deve conter `radius` em Propriedades. `radius` valor é medido em metros, o valor de `radius` varia de 1 a 10000.
* O recurso com `polygon` e `multipolygon` tipo geometry não tem uma propriedade RADIUS.
* `validityTime` é uma propriedade opcional que permite que o usuário defina a hora expirada e o período de tempo de validade para os dados de limite geográfico. Se não for especificado, os dados nunca expirarão e serão sempre válidos.
* A `expiredTime` é a data e a hora de expiração dos dados de isolamento geográfica. Se o valor de `userTime` na solicitação for posterior a esse valor, os dados de cerca geográfica correspondentes serão considerados como dados expirados e não serão consultados. Após o qual, a geometryid desses dados geométricos serão incluídos na matriz de `expiredGeofenceGeometryId` dentro da resposta de cerca geográfica.
* O `validityPeriod` é uma lista de períodos de tempo de validade da cerca geográfica. Se o valor de `userTime` na solicitação cair fora do período de validade, os dados de cerca geográfica correspondentes serão considerados inválidos e não serão consultados. A geometryid desses dados de cerca geográfica está incluída na matriz de `invalidPeriodGeofenceGeometryId` dentro da resposta de cerca geográfica. A tabela a seguir mostra as propriedades do elemento validityPeriod.

| Nome | Tipo | Obrigatório  | Descrição |
| :------------ |:------------: |:---------------:| :-----|
| startTime | DateTime  | true | A data e a hora de início do período de validade. |
| endTime   | DateTime  | true |  A data e a hora de término do período de validade. |
| recurrenceType | string | false |   O tipo de recorrência do período. O valor pode ser `Daily`, `Weekly`, `Monthly`ou `Yearly`. O valor padrão é `Daily`.|
| businessDayOnly | Booleano | false |  Indique se os dados são válidos somente durante os dias úteis. O valor padrão é `false`.|


* Todos os valores de coordenadas são representados como [longitude, latitude] definido em `WGS84`.
* Para cada recurso, que contém `MultiPoint`, `MultiLineString`, `MultiPolygon` ou `GeometryCollection`, as propriedades são aplicadas a todos os elementos. por exemplo: todos os pontos em `MultiPoint` usarão o mesmo raio para formar um limite geográfico de vários círculos.
* No cenário de círculo de ponto, uma geometria de círculo pode ser representada usando um objeto `Point` Geometry com propriedades elaboradas na [extensão de geometrias geojson](https://docs.microsoft.com/azure/azure-maps/extend-geojson).      

Veja a seguir um corpo de solicitação de exemplo para uma cerca geográfica representada como uma geometria geográfica de círculo em `GeoJSON` usando um ponto central e um raio. O período válido dos dados de limite geográfico começa de 2018-10-22, 9:00 às 17:00, repetido todos os dias, exceto o fim de semana. `expiredTime` indica que esses dados de cerca geográfica serão considerados expirados, se `userTime` na solicitação for posterior à `2019-01-01`.  

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
