---
title: Adicione uma camada de extrusão de polígono a um mapa Microsoft Azure Maps
description: Como adicionar uma camada de extrusão de polígono ao Microsoft Azure Maps Web SDK.
author: anastasia-ms
ms.author: v-stharr
ms.date: 10/08/2019
ms.topic: conceptual
ms.service: azure-maps
services: azure-maps
manager: ''
ms.custom: codepen, devx-track-js
ms.openlocfilehash: eedbbc0126adacc2a9bdc151aa6dbc27c7ba0750
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/09/2020
ms.locfileid: "91310259"
---
# <a name="add-a-polygon-extrusion-layer-to-the-map"></a>Adicione uma camada de extrusão de polígono ao mapa

Este artigo mostra-lhe como usar a camada de extrusão do polígono para tornar áreas `Polygon` e `MultiPolygon` características geometrias como formas extrudidas. O Azure Maps Web SDK suporta a renderização de geometrias circle, tal como definido no [esquema alargado de GeoJSON.](extend-geojson.md#circle) Estes círculos podem ser transformados em polígonos quando renderizados no mapa. Todas as geometrias de características podem ser facilmente atualizadas quando embrulhadas com o [atlas. Classe de](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.shape) forma.

## <a name="use-a-polygon-extrusion-layer"></a>Use uma camada de extrusão de polígono

Ligue a [camada de extrusão do polígono](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.layer.polygonextrusionlayer) a uma fonte de dados. Depois, carreguei-o no mapa. A camada de extrusão do polígono torna as áreas de um `Polygon` e `MultiPolygon` apresenta-se como formas extrudidas. As  `height` propriedades e propriedades da camada de `base` extrusão do polígono definem a distância de base do solo e da altura da forma extrudida em **metros**. O código que se segue mostra como criar um polígono, adicioná-lo a uma fonte de dados, e torná-lo usando a classe de camada de extrusão do Polygon.

> [!Note]
> O `base` valor definido na camada de extrusão do polígono deve ser inferior ou igual ao da `height` .

<br/>

<iframe height="500" style="width: 100%;" scrolling="no" title="Polígono extrudido" src="https://codepen.io/azuremaps/embed/wvvBpvE?height=265&theme-id=0&default-tab=js,result&editable=true" frameborder='no' loading="lazy" allowtransparency="true" allowfullscreen="true">
Veja o <a href='https://codepen.io/azuremaps/pen/wvvBpvE'>polígono Pen Extruded</a> by Azure Maps <a href='https://codepen.io/azuremaps'>@azuremaps</a> () no <a href='https://codepen.io'>CodePen</a>.</iframe>


## <a name="add-data-driven-polygons"></a>Adicionar polígonos orientados a dados

Um mapa de choropleth pode ser renderizado usando a camada de extrusão do polígono. Definir as `height` propriedades e propriedades da camada de `fillColor` extrusão para a medição da variável estatística nas `Polygon` `MultiPolygon` geometrias e características. A seguinte amostra de código mostra um mapa extrudido do choropleth dos EUA com base na medição da densidade populacional por estado.

<br/>

<iframe height="500" style="width: 100%;" scrolling="no" title="Mapa do choropleth extrudido" src="https://codepen.io/azuremaps/embed/eYYYNox?height=265&theme-id=0&default-tab=result&editable=true" frameborder='no' loading="lazy" allowtransparency="true" allowfullscreen="true">
Veja o mapa do choropleth da Pen <a href='https://codepen.io/azuremaps/pen/eYYYNox'>Extruded</a> por Azure Maps <a href='https://codepen.io/azuremaps'>@azuremaps</a> no <a href='https://codepen.io'>CodePen.</a>
</iframe>

## <a name="add-a-circle-to-the-map"></a>Adicione um círculo ao mapa

O Azure Maps utiliza uma versão estendida do esquema GeoJSON que fornece uma definição para círculos como aqui [se](https://docs.microsoft.com/azure/azure-maps/extend-geojson#circle)nota. Um círculo extrudido pode ser renderizado no mapa criando uma `point` característica com uma propriedade e uma propriedade `subType` `Circle` numerada `Radius` representando o raio em **metros**. Por exemplo:

```javascript
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

O Azure Maps Web SDK converte estas `Point` funcionalidades em `Polygon` funcionalidades sob o capot. Estas `Point` características podem ser renderizadas no mapa utilizando a camada de extrusão do polígono, como mostra a seguinte amostra de código.

<br/>

<iframe height="500" style="width: 100%;" scrolling="no" title="Polígono do espaço aéreo de drones" src="https://codepen.io/azuremaps/embed/zYYYrxo?height=265&theme-id=0&default-tab=js,result&editable=true" frameborder='no' loading="lazy" allowtransparency="true" allowfullscreen="true">
Veja o <a href='https://codepen.io/azuremaps/pen/zYYYrxo'>polígono do espaço aéreo</a> Pen Drone by Azure Maps <a href='https://codepen.io/azuremaps'>@azuremaps</a> () no <a href='https://codepen.io'>CodePen</a>.
</iframe>

## <a name="customize-a-polygon-extrusion-layer"></a>Personalize uma camada de extrusão de polígono

A camada de extrusão polygon tem várias opções de estilo. Aqui está uma ferramenta para experimentá-los.

<br/>

<iframe height='700' scrolling='no' title='PoogBRJ' src='//codepen.io/azuremaps/embed/PoogBRJ/?height=700&theme-id=0&default-tab=result' frameborder='no' loading="lazy" allowtransparency='true' allowfullscreen='true' style='width: 100%;'>Veja o Pen <a href='https://codepen.io/azuremaps/pen/PoogBRJ/'>PoogBRJ</a> by Azure Maps <a href='https://codepen.io/azuremaps'>@azuremaps</a> ( ) no <a href='https://codepen.io'>CodePen</a>.
</iframe>

## <a name="next-steps"></a>Passos seguintes

Saiba mais sobre as aulas e métodos utilizados neste artigo:

> [!div class="nextstepaction"]
> [Polígono](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.data.polygon)

> [!div class="nextstepaction"]
> [camada de extrusão de polígono](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.layer.polygonextrusionlayer)

Recursos adicionais:

> [!div class="nextstepaction"]
> [Extensão de especificação Azure Maps GeoJSON](extend-geojson.md#circle)
