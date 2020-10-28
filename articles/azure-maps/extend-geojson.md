---
title: Geometrias GeoJSON estendidas Microsoft Azure Maps
description: Saiba como o Azure Maps estende a especificação GeoJSON para incluir formas geométricas adicionais. Veja exemplos que configuram círculos e retângulos para uso nos mapas.
author: sataneja
ms.author: sataneja
ms.date: 05/17/2018
ms.topic: conceptual
ms.service: azure-maps
services: azure-maps
manager: ''
ms.openlocfilehash: 87a5dec2582796c6703bfb37b76e26e53a1192c0
ms.sourcegitcommit: 4064234b1b4be79c411ef677569f29ae73e78731
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/28/2020
ms.locfileid: "92895788"
---
# <a name="extended-geojson-geometries"></a>Geometrias geoJSON estendidas

O Azure Maps fornece uma lista de APIs poderosas para pesquisar dentro e ao longo de características geográficas. Estas APIs aderem à [especificação padrão da GeoJSON][1] de representar características geográficas.  

A [especificação GeoJSON][1] suporta apenas as seguintes geometrias:

* GeometriaCollection
* LineString
* MultiLineString
* MultiPoint
* MultiPolygon
* Ponto
* Polígono

Algumas APIs de Mapas Azure aceitam geometrias que não fazem parte da [especificação GeoJSON.][1] Por exemplo, a [API de Geometria Interna](/rest/api/maps/search/postsearchinsidegeometry) aceita Circle e Polygons.

Este artigo fornece uma explicação detalhada sobre como o Azure Maps estende a [especificação GeoJSON][1] para representar certas geometrias.

## <a name="circle"></a>Círculo

A `Circle` geometria não é suportada pela [especificação GeoJSON.][1] Usamos um `GeoJSON Point Feature` objeto para representar um círculo.

Uma `Circle` geometria representada através do `GeoJSON Feature` objeto __deve__ conter as seguintes coordenadas e propriedades:

- Centro

    O centro do círculo é representado usando um `GeoJSON Point` objeto.

- Radius

    O círculo `radius` é representado usando `GeoJSON Feature` as propriedades. O valor do raio está nos _metros_ e deve ser do tipo `double` .

- Subtipo

    A geometria do círculo também deve conter a `subType` propriedade. Esta propriedade deve fazer parte das propriedades e seu `GeoJSON Feature` valor deve ser _Circle_

#### <a name="example"></a>Exemplo

Eis como vai representar um círculo usando um `GeoJSON Feature` objeto. Vamos centrar o círculo em latitude: 47.639754 e longitude: -122.126986, e atribuir-lhe um raio igual a 100 metros:

```json            
{
    "type": "Feature",
    "geometry": {
        "type": "Point",
        "coordinates": [-122.126986, 47.639754]
    },
    "properties": {
        "subType": "Circle",
        "radius": 100
    }
}          
```

## <a name="rectangle"></a>Retângulo

A `Rectangle` geometria não é suportada pela [especificação GeoJSON.][1] Usamos um `GeoJSON Polygon Feature` objeto para representar um retângulo. A extensão do retângulo é utilizada principalmente pelo módulo de ferramentas de desenho da Web SDK.

Uma `Rectangle` geometria representada através do `GeoJSON Polygon Feature` objeto __deve__ conter as seguintes coordenadas e propriedades:

- Cantos

    Os cantos do retângulo são representados usando as coordenadas de um `GeoJSON Polygon` objeto. Deve haver cinco coordenadas, uma para cada canto. E uma quinta coordenada que é a mesma que a primeira coordenada. Será assumido que estas coordenadas se alinham, e que o desenvolvedor pode rodá-las como desejado.

- Subtipo

    A geometria do retângulo também deve conter a `subType` propriedade. Esta propriedade deve ser uma parte das `GeoJSON Feature` propriedades, e seu valor deve ser _Retângulo_

### <a name="example"></a>Exemplo

```json
{
    "type": "Feature",
    "geometry": {
        "type": "Polygon",
        "coordinates": [[[5,25],[14,25],[14,29],[5,29],[5,25]]]
    },
    "properties": {
        "subType": "Rectangle"
    }
}

```
## <a name="next-steps"></a>Passos seguintes

Saiba mais sobre os dados da GeoJSON no Azure Maps:

> [!div class="nextstepaction"]
> [Formato GeoJSON de Geofence](geofence-geojson.md)

Reveja o glossário dos termos técnicos comuns associados ao Azure Maps e aplicações de inteligência de localização:

> [!div class="nextstepaction"]
> [Glossário do Azure Maps](glossary.md)

[1]: https://tools.ietf.org/html/rfc7946