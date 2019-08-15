---
title: Adicionar uma camada de bolha ao Azure Maps | Microsoft Docs
description: Como adicionar uma camada de bolha ao SDK da Web do Azure Maps.
author: rbrundritt
ms.author: richbrun
ms.date: 07/29/2019
ms.topic: conceptual
ms.service: azure-maps
services: azure-maps
manager: ''
ms.custom: codepen
ms.openlocfilehash: 5cc5dbdc89f629c09d47ef683b7ff7fff61d2f49
ms.sourcegitcommit: 62bd5acd62418518d5991b73a16dca61d7430634
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 08/13/2019
ms.locfileid: "68976583"
---
# <a name="add-a-bubble-layer-to-a-map"></a>Adicionar uma camada de bolha a um mapa

Este artigo mostra como você pode renderizar dados de ponto de uma fonte de dados como uma camada de bolha em um mapa. Camadas de bolha renderizam pontos como círculos no mapa com raio de pixel fixo. 

> [!TIP]
> As camadas de bolha por padrão renderizarão as coordenadas de todas as geometrias em uma fonte de dados. Para limitar a camada de modo que ela só processe os recursos de geometria de `filter` ponto, defina a propriedade `['==', ['geometry-type'], 'Point']` da `['any', ['==', ['geometry-type'], 'Point'], ['==', ['geometry-type'], 'MultiPoint']]` camada como ou se você quiser incluir recursos do MultiPoint também.

## <a name="add-a-bubble-layer"></a>Adicionar uma camada de bolha

O código a seguir carrega uma matriz de pontos em uma fonte de dados e conecta-o a uma [camada de bolha](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.layer.bubblelayer?view=azure-iot-typescript-latest). A camada de bolha recebe opções para renderizar o raio de cada bolha em cinco pixels, uma cor de preenchimento branca, uma cor de traço de azul e largura de traço de seis pixels. 

```javascript
//Add point locations.
var points = [
    new atlas.data.Point([-73.985708, 40.75773]),
    new atlas.data.Point([-73.985600, 40.76542]),
    new atlas.data.Point([-73.985550, 40.77900]),
    new atlas.data.Point([-73.975550, 40.74859]),
    new atlas.data.Point([-73.968900, 40.78859])
];

//Create a data source and add it to the map.
var dataSource = new atlas.source.DataSource();
map.sources.add(dataSource);

//Add multiple points to the data source.
dataSource.add(points);

//Create a bubble layer to render the filled in area of the circle, and add it to the map.
map.layers.add(new atlas.layer.BubbleLayer(dataSource, null, {
    radius: 5,
    strokeColor: "#4288f7",
    strokeWidth: 6, 
    color: "white" 
}));
```

Abaixo está o exemplo de código completo em execução da funcionalidade acima.

<br/>

<iframe height='500' scrolling='no' title='Fonte de BubbleLayer' src='//codepen.io/azuremaps/embed/mzqaKB/?height=500&theme-id=0&default-tab=js,result&embed-version=2&editable=true' frameborder='no' allowtransparency='true' allowfullscreen='true' style='width: 100%;'>Consulte a caneta <a href='https://codepen.io/azuremaps/pen/mzqaKB/'>BubbleLayer DataSource</a> pelo Azure Maps (<a href='https://codepen.io/azuremaps'>@azuremaps</a>) em <a href='https://codepen.io'>CodePen</a>.
</iframe>

## <a name="show-labels-with-a-bubble-layer"></a>Mostrar rótulos com uma camada de bolha

O código a seguir mostra como usar uma camada de bolha para renderizar um ponto no mapa e uma camada de símbolo para renderizar um rótulo. Para ocultar o ícone da camada de símbolo, defina a `image` propriedade das opções de ícone como `'none'`.

<br/>

<iframe height='500' scrolling='no' title='Fonte de fontes multicamadas' src='//codepen.io/azuremaps/embed/rqbQXy/?height=500&theme-id=0&default-tab=js,result&embed-version=2&editable=true' frameborder='no' allowtransparency='true' allowfullscreen='true' style='width: 100%;'>Consulte a caneta <a href='https://codepen.io/azuremaps/pen/rqbQXy/'>multicamada DataSource</a> por mapas do<a href='https://codepen.io/azuremaps'>@azuremaps</a>Azure () em <a href='https://codepen.io'>CodePen</a>.
</iframe>

## <a name="customize-a-bubble-layer"></a>Personalizar uma camada de bolha

A camada de bolha tem apenas algumas opções de estilo. Aqui está uma ferramenta para experimentá-las.

<br/>

<iframe height='700' scrolling='no' title='Opções de camada de bolha' src='//codepen.io/azuremaps/embed/eQxbGm/?height=700&theme-id=0&default-tab=result' frameborder='no' allowtransparency='true' allowfullscreen='true' style='width: 100%;'>Consulte as <a href='https://codepen.io/azuremaps/pen/eQxbGm/'>Opções da camada de bolha</a> da caneta pelo<a href='https://codepen.io/azuremaps'>@azuremaps</a>Azure Maps () em <a href='https://codepen.io'>CodePen</a>.
</iframe>

## <a name="next-steps"></a>Passos Seguintes

Saiba mais sobre as classes e os métodos usados neste artigo:

> [!div class="nextstepaction"]
> [BubbleLayer](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.layer.bubblelayer?view=azure-iot-typescript-latest)

> [!div class="nextstepaction"]
> [BubbleLayerOptions](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.bubblelayeroptions?view=azure-iot-typescript-latest)

Consulte os artigos a seguir para obter mais exemplos de código para adicionar aos seus mapas:

> [!div class="nextstepaction"]
> [Criar uma fonte de dados](create-data-source-web-sdk.md)

> [!div class="nextstepaction"]
> [Adicionar uma camada de símbolo](map-add-pin.md)

> [!div class="nextstepaction"]
> [Usar expressões de estilo controladas por dados](data-driven-style-expressions-web-sdk.md)

> [!div class="nextstepaction"]
> [Exemplos de código](https://docs.microsoft.com/samples/browse/?products=azure-maps)