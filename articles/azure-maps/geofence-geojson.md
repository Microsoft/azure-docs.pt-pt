---
title: Formato de dados do perímetro geográfico GeoJSON no Azure Maps | Documentos da Microsoft
description: Saiba mais sobre o formato de dados do perímetro geográfico GeoJSON no Azure Maps
author: walsehgal
ms.author: v-musehg
ms.date: 02/14/2019
ms.topic: conceptual
ms.service: azure-maps
services: azure-maps
manager: ''
ms.openlocfilehash: 6f76eea365311cd6c10dd39054187bf3a07bd595
ms.sourcegitcommit: dd1a9f38c69954f15ff5c166e456fda37ae1cdf2
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/07/2019
ms.locfileid: "57568996"
---
# <a name="geofencing-geojson-data"></a>Dados de barreira geográfica GeoJSON

O Azure Maps [obter perímetro geográfico](https://docs.microsoft.com/rest/api/maps/spatial/getgeofencepreview) e [perímetro geográfico de POSTAGEM](https://docs.microsoft.com/rest/api/maps/spatial/postgeofencepreview) APIs permitem-lhe obter proximidade de uma coordenada em relação a um perímetro geográfico fornecido ou conjunto de limites. Este artigo fornece detalhes sobre como preparar os dados do perímetro geográfico que podem ser utilizados na introdução do Azure Maps e API de publicação.

Os dados para o perímetro geográfico ou conjunto de perímetros geográficos são representados por `Feature` objeto e `FeatureCollection` objeto `GeoJSON` formato, o que é definido no [rfc7946](https://tools.ietf.org/html/rfc7946). Além de que:

* O tipo de objeto GeoJSON pode ser um `Feature` objeto ou uma `FeatureCollection` objeto.
* O tipo de objeto Geometry pode ser um `Point`, `MultiPoint`, `LineString`, `MultiLineString`, `Polygon`, `MultiPolygon`, e `GeometryCollection`.
* Todas as propriedades de recurso devem conter um `geometryId`, que é utilizado para identificar o perímetro geográfico.
* Funcionalidade com `Point`, `MultiPoint`, `LineString`, `MultiLineString` tem de conter `radius` nas propriedades. `radius` valor é medido em medidores, o `radius` vai de 1 a 10000 de valor.
* Funcionalidade com `polygon` e `multipolygon` tipo geometria não tem uma propriedade radius.
* `validityTime` é uma propriedade opcional que permite que o utilizador definir período expirado e validade período de tempo para os dados do perímetro geográfico. Se não for especificado, os dados nunca expira e é sempre válidos.
* O `expiredTime` é a data de expiração e hora de dados de barreira geográfica. Se o valor de `userTime` no pedido é posterior a este valor, do perímetro geográfico correspondente dados são considerados dados expirados e não são consultados. Sobre a qual, geometryId deste perímetro geográfico dados serão incluídos no `expiredGeofenceGeometryId` matriz da resposta do perímetro geográfico.
* O `validityPeriod` é uma lista de validade período de tempo do perímetro geográfico. Se o valor de `userTime` cai a pedido, fora do período de validade, os dados correspondentes do perímetro geográfico são considerados como inválidos e não serão consultados. GeometryId destes dados de perímetro geográfico está incluído no `invalidPeriodGeofenceGeometryId` matriz dentro do perímetro geográfico resposta. A tabela seguinte mostra as propriedades do elemento de validityPeriod.

| Name | Type | Necessário  | Descrição |
| :------------ |:------------: |:---------------:| :-----|
| startTime | Datetime  | true | O início, data hora da validade período de tempo. |
| endTime   | Datetime  | true |  Data hora da validade período de tempo final. |
| recurrenceType | string | false |   O tipo de periodicidade do período. O valor pode ser `Daily`, `Weekly`, `Monthly`, ou `Yearly`. Valor predefinido é `Daily`.|
| businessDayOnly | Booleano | false |  Indica se os dados só são válidos durante dias úteis. Valor predefinido é `false`.|


* Todos os valores de coordenadas são representados como [latitude, longitude] definido na `WGS84`.
* Para cada funcionalidade, que contém `MultiPoint`, `MultiLineString`, `MultiPolygon` , ou `GeometryCollection`, as propriedades são aplicadas a todos os elementos. Por exemplo: Todos os pontos no `MultiPoint` irá utilizar o mesmo radius para formar um perímetro de geográfico vários do círculo.
* Cenário de ponto-círculo, uma geometria de círculo pode ser representada com um `Point` objeto geometry com propriedades fosse elaborado de acordo na [estendendo GeoJSON geometrias](https://docs.microsoft.com/azure/azure-maps/extend-geojson).      

Segue-se um corpo de pedido de exemplo para um perímetro geográfico representado como uma geometria de perímetro geográfico do círculo no `GeoJSON` usando um ponto central e um raio. O período válido dos dados do perímetro geográfico começa a partir de 2018-10-22, às 09: para 5 PM, repetido todos os dias, exceto para o fim de semana. `expiredTime` indica estes dados de perímetro geográfico serão considerados expirou, se `userTime` no pedido é posterior à `2019-01-01`.  

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