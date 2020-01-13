---
title: Geometrias geojson estendidas | Mapas do Microsoft Azure
description: Neste artigo, você aprenderá como o Microsoft Azure Maps estende a especificação geojson para representar determinadas geometrias.
author: sataneja
ms.author: sataneja
ms.date: 05/17/2018
ms.topic: conceptual
ms.service: azure-maps
services: azure-maps
manager: ''
ms.openlocfilehash: 75ad83411edfdfe7545e8f80df17fea56e317ee0
ms.sourcegitcommit: f9601bbccddfccddb6f577d6febf7b2b12988911
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 01/12/2020
ms.locfileid: "75911637"
---
# <a name="extended-geojson-geometries"></a>Geometrias geojson estendidas

O mapas do Azure fornece uma lista de APIs poderosas para pesquisar dentro/ao longo dos recursos geográficos.
Essas APIs padronizam a [especificação geojson][1] para representar os recursos geográficos (por exemplo: limites de estado, rotas).  

A [especificação geojson][1] só dá suporte às seguintes geometrias:

* GeometryCollection
* LineString
* MultiLineString
* MultiPoint
* MultiPolygon
* Ponto
* Polygon

Algumas APIs do Azure Maps (por exemplo: [Pesquisar dentro da geometria](https://docs.microsoft.com/rest/api/maps/search/postsearchinsidegeometry)) aceitam geometrias como "Circle", que não fazem parte da [especificação geojson][1].

Este artigo fornece uma explicação detalhada sobre como o Azure Maps estende a [especificação geojson][1] para representar determinadas geometrias.

## <a name="circle"></a>Círculo

Não há suporte para a geometria `Circle`da pela [especificação geojson][1]. Usamos um objeto `GeoJSON Point Feature` para representar um círculo.

Uma geometria `Circle` representada usando o objeto `GeoJSON Feature` __deve__ conter o seguinte:

- Centro do

    O centro do círculo é representado usando um objeto `GeoJSON Point`.

- Raio

    O `radius` do círculo é representado usando as propriedades de `GeoJSON Feature`. O valor de raio está em _metros_ e deve ser do tipo `double`.

- Subtipo

    A geometria de círculo também tem de conter a propriedade `subType`. Essa propriedade deve ser uma parte das propriedades do `GeoJSON Feature`e seu valor deve ser _Circle_

#### <a name="example"></a>Exemplo

Veja como você representará um círculo centralizado em (Latitude: 47,639754, Longitude:-122,126986) com um raio igual a 100 metros, usando um objeto `GeoJSON Feature`:

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

Não há suporte para a geometria `Rectangle`da pela [especificação geojson][1]. Usamos um objeto `GeoJSON Polygon Feature` para representar um retângulo. A extensão de retângulo é usada principalmente pelo módulo de ferramentas de desenho do SDK da Web.

Uma geometria `Rectangle` representada usando o objeto `GeoJSON Polygon Feature` __deve__ conter o seguinte:

- Corner

    Os cantos do retângulo são representados usando as coordenadas de um objeto `GeoJSON Polygon`. Deve haver cinco coordenadas, uma para cada canto e uma quinta coordenada igual à primeira para fechar o anel do polígono. Essas coordenadas serão consideradas alinhadas e giradas conforme desejado pelo desenvolvedor.

- Subtipo

    A geometria do retângulo também deve conter a propriedade `subType`. Essa propriedade deve ser uma parte das propriedades do `GeoJSON Feature`e seu valor deve ser _Rectangle_

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

Saiba mais sobre dados geojson no Azure Maps:

> [!div class="nextstepaction"]
> [Formato geojson de limite geográfico](geofence-geojson.md)

Examine o Glossário de termos técnicos comuns associados a aplicativos do Azure Maps e do local Intelligence:

> [!div class="nextstepaction"]
> [Glossário do Azure Maps](glossary.md)

[1]: https://tools.ietf.org/html/rfc7946
