---
title: Geometrias GeoJSON Estendidas / Microsoft Azure Maps
description: Neste artigo, você vai aprender sobre como o Microsoft Azure Maps estende a especificação GeoJSON para representar certas geometrias.
author: sataneja
ms.author: sataneja
ms.date: 05/17/2018
ms.topic: conceptual
ms.service: azure-maps
services: azure-maps
manager: ''
ms.openlocfilehash: 98db10f0fc7a417f39d4bb00e77af6bdea034a03
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/28/2020
ms.locfileid: "79276403"
---
# <a name="extended-geojson-geometries"></a>Geometrias GeoJSON estendidas

O Azure Maps fornece uma lista de APIs poderosos para pesquisar dentro e ao longo de características geográficas. Estas APIs aderem à [especificação padrão geoJSON][1] de representar características geográficas.  

A [especificação GeoJSON][1] suporta apenas as seguintes geometrias:

* GeometriaColeção
* Linha string
* Cadeia Multiline
* MultiPoint
* MultiPolygon
* Ponto
* Polígono

Algumas APIs do Azure Maps aceitam geometrias que não fazem parte da [especificação GeoJSON.][1] Por exemplo, a API de Pesquisa Dentro de [Geometria](https://docs.microsoft.com/rest/api/maps/search/postsearchinsidegeometry) aceita Círculo e Polígonos.

Este artigo fornece uma explicação detalhada sobre como o Azure Maps alarga a [especificação GeoJSON][1] para representar certas geometrias.

## <a name="circle"></a>Círculo

A `Circle` geometria não é suportada pela [especificação GeoJSON.][1] Usamos um `GeoJSON Point Feature` objeto para representar um círculo.

Uma `Circle` geometria representada `GeoJSON Feature` com o objeto __deve__ conter as seguintes coordenadas e propriedades:

- Centro

    O centro do círculo é `GeoJSON Point` representado usando um objeto.

- Raio

    O círculo `radius` é representado `GeoJSON Feature`usando as propriedades do círculo. O valor do _meters_ raio é de `double`metros e deve ser do tipo .

- SubTipo

    A geometria do `subType` círculo também deve conter a propriedade. Esta propriedade deve fazer `GeoJSON Feature`parte das propriedades e o seu valor deve ser _circle_

#### <a name="example"></a>Exemplo

Eis como vai representar um círculo `GeoJSON Feature` usando um objeto. Vamos centrar o círculo na latitude: 47.639754 e longitude: -122.126986, e atribuir-lhe um raio igual a 100 metros:

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

A `Rectangle` geometria não é suportada pela [especificação GeoJSON.][1] Usamos um `GeoJSON Polygon Feature` objeto para representar um retângulo. A extensão do retângulo é utilizada principalmente pelo módulo de ferramentas de desenho do Web SDK.

Uma `Rectangle` geometria representada `GeoJSON Polygon Feature` com o objeto __deve__ conter as seguintes coordenadas e propriedades:

- Cantos

    Os cantos do retângulo são representados usando `GeoJSON Polygon` as coordenadas de um objeto. Deve haver cinco coordenadas, uma para cada canto. E uma quinta coordenada que é a mesma que a primeira coordenada, para fechar o anel de polígono. Supõe-se que estas coordenadas se alinham e que o desenvolvedor pode rodá-las como desejado.

- SubTipo

    A geometria do retângulo também deve conter a `subType` propriedade. Esta propriedade deve ser `GeoJSON Feature`uma parte das propriedades, e seu valor deve ser _Rectangle_

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
> [Formato Geofence GeoJSON](geofence-geojson.md)

Reveja o glossário de termos técnicos comuns associados ao Azure Maps e aplicações de inteligência de localização:

> [!div class="nextstepaction"]
> [Glossário do Azure Maps](glossary.md)

[1]: https://tools.ietf.org/html/rfc7946
