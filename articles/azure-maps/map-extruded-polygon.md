---
title: Adicione uma camada de extrusão de polígono a um mapa Microsoft Azure Maps
description: Como adicionar uma camada de extrusão de poligão ao Microsoft Azure Maps Web SDK.
author: farah-alyasari
ms.author: v-faalya
ms.date: 10/08/2019
ms.topic: conceptual
ms.service: azure-maps
services: azure-maps
manager: ''
ms.custom: codepen
ms.openlocfilehash: 38a7e6c5f4c139343e735ae82616c11a224ae7ca
ms.sourcegitcommit: 2823677304c10763c21bcb047df90f86339e476a
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 02/14/2020
ms.locfileid: "77209686"
---
# <a name="add-a-polygon-extrusion-layer-to-the-map"></a>Adicione uma camada de extrusão de poligão ao mapa

Este artigo mostra como usar a camada de extrusão de poligão para tornar áreas de `Polygon` e `MultiPolygon` características geometrias como formas extrudidas. O Azure Maps Web SDK suporta a renderização das geometrias Circle, tal como definida no [esquema geojson alargado](extend-geojson.md#circle). Estes círculos podem ser transformados em polígonos quando prestados no mapa. Todas as geometrias de recursos podem ser atualizadas facilmente quando embrulhadas com o [atlas. Classe](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.shape?view=azure-iot-typescript-latest) de forma.

## <a name="use-a-polygon-extrusion-layer"></a>Use uma camada de extrusão de poligão

Ligue a camada de [extrusão do polígono](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.layer.polygonextrusionlayer?view=azure-maps-typescript-latest) a uma fonte de dados. Depois, carregou-o no mapa. A camada de extrusão do polígono torna as áreas de um `Polygon` e `MultiPolygon` características como formas extrudidas. As propriedades `height` e `base` da camada de extrusão do polígono definem a distância base do solo e da altura da forma extrudida em **metros.** O código seguinte mostra como criar um polígono, adicioná-lo a uma fonte de dados, e torná-lo usando a classe de camada de extrusão polygon.

> [!Note]
> O valor `base` definido na camada de extrusão do polígono deve ser inferior ou igual ao da `height`.

<br/>

<iframe height="500" style="width: 100%;" scrolling="no" title="Polígono extrudido" src="https://codepen.io/azuremaps/embed/wvvBpvE?height=265&theme-id=0&default-tab=js,result&editable=true" frameborder="no" allowtransparency="true" allowfullscreen="true">
Veja o <a href='https://codepen.io/azuremaps/pen/wvvBpvE'>polígono Pen Extrudid</a> o azure Maps<a href='https://codepen.io/azuremaps'> (@azuremaps</a>) no <a href='https://codepen.io'>CodePen</a>.</iframe>


## <a name="add-data-driven-polygons"></a>Adicionar polígonos orientados para dados

Um mapa de choropleth pode ser renderizado usando a camada de extrusão de poligão. Detete as propriedades `height` e `fillColor` da camada de extrusão para a medição da variável estatística no `Polygon` e `MultiPolygon` característica sinuosidades. A amostra de código que se segue mostra um mapa extrudido de coropleth dos EUA com base na medição da densidade populacional por estado.

<br/>

<iframe height="500" style="width: 100%;" scrolling="no" title="Mapa do coropleth extrudido" src="https://codepen.io/azuremaps/embed/eYYYNox?height=265&theme-id=0&default-tab=result&editable=true" frameborder="no" allowtransparency="true" allowfullscreen="true">
Veja o mapa do <a href='https://codepen.io/azuremaps/pen/eYYYNox'>coropleth Pen Extruded</a> da Azure Maps(<a href='https://codepen.io/azuremaps'>@azuremaps) </a>em <a href='https://codepen.io'>CodePen.</a>
</iframe>

## <a name="add-a-circle-to-the-map"></a>Adicione um círculo ao mapa

O Azure Maps utiliza uma versão estendida do esquema GeoJSON que fornece uma definição para círculos como [aqui](https://docs.microsoft.com/azure/azure-maps/extend-geojson#circle)se nota . Um círculo extrudido pode ser renderizado no mapa criando uma característica `point` com uma propriedade `subType` de `Circle` e uma propriedade `Radius` numerada representando o raio em **metros.** Por exemplo:

```Javascript
{
    "type": "Feature",
    "geometry": {
        "type": "Point",
        "coordinates": [-105.203135, 39.664087]
    },
    "properties": {
        "subType": "Circle",
        "radius": 1000
    }
} 
```

O Azure Maps Web SDK converte estas funcionalidades `Point` em funcionalidades `Polygon` sob o capot. Estas características `Point` podem ser renderizadas no mapa utilizando camada de extrusão de poligão, como mostrado na amostra de código seguinte.

<br/>

<iframe height="500" style="width: 100%;" scrolling="no" title="Polígono do espaço aéreo drone" src="https://codepen.io/azuremaps/embed/zYYYrxo?height=265&theme-id=0&default-tab=js,result&editable=true" frameborder="no" allowtransparency="true" allowfullscreen="true">
Veja o <a href='https://codepen.io/azuremaps/pen/zYYYrxo'>polígono do espaço aéreo</a> Pen Drone por Azure Maps<a href='https://codepen.io/azuremaps'> (@azuremaps</a>) no <a href='https://codepen.io'>CodePen</a>.
</iframe>

## <a name="customize-a-polygon-extrusion-layer"></a>Personalize uma camada de extrusão de poligão

A camada de Extrusão Polygon tem várias opções de estilo. Aqui está uma ferramenta para experimentá-los.

<br/>

<iframe height='700' scrolling='no' title='PoogBRJ' src='//codepen.io/azuremaps/embed/PoogBRJ/?height=700&theme-id=0&default-tab=result' frameborder='no' allowtransparency='true' allowfullscreen='true' style='width: 100%;'>Consulte a Pen <a href='https://codepen.io/azuremaps/pen/PoogBRJ/'>PoogBRJ</a> by Azure Maps<a href='https://codepen.io/azuremaps'> (@azuremaps</a>) no <a href='https://codepen.io'>CodePen</a>.
</iframe>

## <a name="next-steps"></a>Passos seguintes

Saiba mais sobre as aulas e métodos utilizados neste artigo:

> [!div class="nextstepaction"]
> [Polígono](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.data.polygon?view=azure-iot-typescript-latest)

> [!div class="nextstepaction"]
> [camada de extrusão de polígono](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.layer.polygonextrusionlayer?view=azure-maps-typescript-latest)

Recursos adicionais:

> [!div class="nextstepaction"]
> [Extensão da especificação Do GeoJSON do Azure Maps](extend-geojson.md#circle)
