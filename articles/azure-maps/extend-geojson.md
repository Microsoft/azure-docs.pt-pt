---
title: Estendendo GeoJSON geometrias do Azure Maps | Documentos da Microsoft
description: Saiba como estender GeoJSON geometrias do Azure Maps
author: sataneja
ms.author: sataneja
ms.date: 05/17/2018
ms.topic: conceptual
ms.service: azure-maps
services: azure-maps
manager: ''
ms.openlocfilehash: be3c31951c4721a861f9239c5220419dec11b6bf
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 06/13/2019
ms.locfileid: "60799144"
---
# <a name="extending-geojson-geometries"></a>Estendendo GeoJSON geometrias

O Azure Maps fornece uma lista de APIs poderosas para pesquisar interior/juntamente com funcionalidades geográficas.
Essas APIs padronizar [especificação GeoJSON] [ 1] para representar os recursos geográficos (por exemplo: limites de estado, as rotas).  

O [especificação GeoJSON] [ 1] só suporta as geometrias seguintes:

* GeometryCollection
* LineString
* MultiLineString
* MultiPoint
* MultiPolygon
* Ponto de
* Polígono

Algumas APIs de mapas do Azure (por exemplo: [Pesquisa dentro de geometria](https://docs.microsoft.com/rest/api/maps/search/postsearchinsidegeometry)) aceitar geometrias como "Círculo", que não são parte dos [especificação GeoJSON][1].

Este artigo fornece uma explicação detalhada sobre como o Azure Maps estende a [especificação GeoJSON] [ 1] para representar a determinados geometrias.

### <a name="circle"></a>Círculo

O `Circle` geometria não é suportada pela [especificação GeoJSON][1]. Usamos o `GeoJSON Feature` objeto para representar um círculo.

Uma `Circle` geometry representada usando o `GeoJSON Feature` objeto __tem__ conter os seguintes:

1. Centro
   >Centro do círculo é representado através de um `GeoJSON Point` tipo.

2. Raio
   >O círculo `radius` é representado através de `GeoJSON Feature`de propriedades. O valor de radius está num _medidores_ e tem de ser do tipo `double`.

3. Subtipo
   >A geometria de círculo também tem de conter a propriedade `subType`. Esta propriedade tem de fazer parte das propriedades de `GeoJSON Feature` e o respetivo valor deverá ser _Circle_


#### <a name="example"></a>Exemplo

Eis como irá representar um círculo centralizado em (latitude: 47.639754, longitude:-122.126986) com um raio igual a 100 metros, usando um `GeoJSON Feature` objeto:

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

[1]: https://tools.ietf.org/html/rfc7946
