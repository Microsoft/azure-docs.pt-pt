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
ms.sourcegitcommit: 333af18fa9e4c2b376fa9aeb8f7941f1b331c11d
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 02/13/2020
ms.locfileid: "77198280"
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

A geometria `Circle` não é suportada pela [especificação GeoJSON.][1] Usamos um objeto `GeoJSON Point Feature` para representar um círculo.

Uma geometria `Circle` representada com o objeto `GeoJSON Feature` __deve__ conter as seguintes coordenadas e propriedades:

- Centro do

    O centro do círculo é representado usando um objeto `GeoJSON Point`.

- raio

    A `radius` do círculo é representada usando as propriedades de `GeoJSON Feature`. O valor do raio é de _contadores_ e deve ser do tipo `double`.

- SubTipo

    A geometria do círculo também deve conter a propriedade `subType`. Esta propriedade deve fazer parte das propriedades do `GeoJSON Feature`e o seu valor deve ser _a Circle_

#### <a name="example"></a>Exemplo

Eis como vai representar um círculo usando um objeto `GeoJSON Feature`. Vamos centrar o círculo na latitude: 47.639754 e longitude: -122.126986, e atribuir-lhe um raio igual a 100 metros:

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

A geometria `Rectangle` não é suportada pela [especificação GeoJSON.][1] Usamos um objeto `GeoJSON Polygon Feature` para representar um retângulo. A extensão do retângulo é utilizada principalmente pelo módulo de ferramentas de desenho do Web SDK.

Uma geometria `Rectangle` representada com o objeto `GeoJSON Polygon Feature` __deve__ conter as seguintes coordenadas e propriedades:

- Cantos

    Os cantos do retângulo são representados usando as coordenadas de um objeto `GeoJSON Polygon`. Deve haver cinco coordenadas, uma para cada canto. E uma quinta coordenada que é a mesma que a primeira coordenada, para fechar o anel de polígono. Supõe-se que estas coordenadas se alinham e que o desenvolvedor pode rodá-las como desejado.

- SubTipo

    A geometria retângulo também deve conter a propriedade `subType`. Esta propriedade deve fazer parte das propriedades do `GeoJSON Feature`, e o seu valor deve ser _Rectangle_

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
> [Glossário Azure Maps](glossary.md)

[1]: https://tools.ietf.org/html/rfc7946
