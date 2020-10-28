---
title: Adicione uma camada de Bolha a um mapa Microsoft Azure Maps
description: Aprenda a renderizar pontos em mapas como círculos com tamanhos fixos. Veja como usar o Azure Maps Web SDK para adicionar e personalizar camadas de bolha para este fim.
author: rbrundritt
ms.author: richbrun
ms.date: 07/29/2019
ms.topic: conceptual
ms.service: azure-maps
services: azure-maps
manager: ''
ms.custom: codepen, devx-track-js
ms.openlocfilehash: cae29dcc0d334a2296199da0d8e3bc4562e275d3
ms.sourcegitcommit: 4064234b1b4be79c411ef677569f29ae73e78731
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/28/2020
ms.locfileid: "92895333"
---
# <a name="add-a-bubble-layer-to-a-map"></a>Adicione uma camada de bolha a um mapa

Este artigo mostra-lhe como fazer dados pontuais a partir de uma fonte de dados como uma camada de bolha num mapa. Camadas de bolhas tornam os pontos como círculos no mapa com um raio de pixel fixo. 

> [!TIP]
> Camadas de bolha por defeito tornarão as coordenadas de todas as geometrias numa fonte de dados. Para limitar a camada de tal forma que apenas torna as características de geometria de ponto definir a `filter` propriedade da camada para ou se você deseja incluir as `['==', ['geometry-type'], 'Point']` `['any', ['==', ['geometry-type'], 'Point'], ['==', ['geometry-type'], 'MultiPoint']]` funcionalidades MultiPoint também.

## <a name="add-a-bubble-layer"></a>Adicionar uma camada de bolha

O código seguinte carrega uma série de pontos numa fonte de dados. Em seguida, liga os pontos de dados a uma [camada de bolha.](/javascript/api/azure-maps-control/atlas.layer.bubblelayer) A camada de bolha torna o raio de cada bolha com cinco pixéis e uma cor de preenchimento de branco. E, uma cor de traçado de azul, e uma largura de curso de seis píxeis. 

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

Abaixo está a amostra completa do código de execução da funcionalidade acima.

<br/>

<iframe height='500' scrolling='no' title='Fonte de dados bubblelayer' src='//codepen.io/azuremaps/embed/mzqaKB/?height=500&theme-id=0&default-tab=js,result&embed-version=2&editable=true' frameborder='no' loading="lazy" allowtransparency='true' allowfullscreen='true' style='width: 100%;'>Consulte o Pen <a href='https://codepen.io/azuremaps/pen/mzqaKB/'>BubbleLayer DataSource</a> by Azure Maps <a href='https://codepen.io/azuremaps'>@azuremaps</a> () no <a href='https://codepen.io'>CodePen</a>.
</iframe>

## <a name="show-labels-with-a-bubble-layer"></a>Mostrar rótulos com uma camada de bolha

Este código mostra como usar uma camada de bolha para tornar um ponto no mapa. E, como usar uma camada de símbolo para renderizar uma etiqueta. Para ocultar o ícone da camada de símbolo, deite a `image` propriedade das opções do ícone para `'none'` .

<br/>

<iframe height='500' scrolling='no' title='Fonte de dados multi-layer' src='//codepen.io/azuremaps/embed/rqbQXy/?height=500&theme-id=0&default-tab=js,result&embed-version=2&editable=true' frameborder='no' loading="lazy" allowtransparency='true' allowfullscreen='true' style='width: 100%;'>Consulte a Pen <a href='https://codepen.io/azuremaps/pen/rqbQXy/'>MultiLayer DataSource</a> by Azure Maps <a href='https://codepen.io/azuremaps'>@azuremaps</a> () no <a href='https://codepen.io'>CodePen</a>.
</iframe>

## <a name="customize-a-bubble-layer"></a>Personalize uma camada de bolha

A camada Bolha tem apenas algumas opções de estilo. Aqui está uma ferramenta para experimentá-los.

<br/>

<iframe height='700' scrolling='no' title='Opções de camada de bolha' src='//codepen.io/azuremaps/embed/eQxbGm/?height=700&theme-id=0&default-tab=result' frameborder='no' loading="lazy" allowtransparency='true' allowfullscreen='true' style='width: 100%;'>Consulte as <a href='https://codepen.io/azuremaps/pen/eQxbGm/'>opções de camada de bolha de caneta</a> por Azure Maps <a href='https://codepen.io/azuremaps'>@azuremaps</a> () no <a href='https://codepen.io'>CodePen</a>.
</iframe>

## <a name="next-steps"></a>Passos seguintes

Saiba mais sobre as aulas e métodos utilizados neste artigo:

> [!div class="nextstepaction"]
> [BubbleLayer](/javascript/api/azure-maps-control/atlas.layer.bubblelayer)

> [!div class="nextstepaction"]
> [BubbleLayerOptions](/javascript/api/azure-maps-control/atlas.bubblelayeroptions)

Consulte os seguintes artigos para obter mais amostras de código para adicionar aos seus mapas:

> [!div class="nextstepaction"]
> [Criar uma origem de dados](create-data-source-web-sdk.md)

> [!div class="nextstepaction"]
> [Adicionar uma camada de símbolo](map-add-pin.md)

> [!div class="nextstepaction"]
> [Utilizar expressões de estilo com base em dados](data-driven-style-expressions-web-sdk.md)

> [!div class="nextstepaction"]
> [Amostras de código](/samples/browse/?products=azure-maps)