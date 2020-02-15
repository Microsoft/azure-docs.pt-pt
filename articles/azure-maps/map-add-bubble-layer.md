---
title: Adicione uma camada de bolha a um mapa Microsoft Azure Maps
description: Neste artigo, você aprenderá sobre como adicionar uma camada bolha a um mapa usando o Microsoft Azure Maps Web SDK.
author: rbrundritt
ms.author: richbrun
ms.date: 07/29/2019
ms.topic: conceptual
ms.service: azure-maps
services: azure-maps
manager: ''
ms.custom: codepen
ms.openlocfilehash: 7ae11734eb804715f3eb1b5edcb02fc328dafec8
ms.sourcegitcommit: 2823677304c10763c21bcb047df90f86339e476a
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 02/14/2020
ms.locfileid: "77208561"
---
# <a name="add-a-bubble-layer-to-a-map"></a>Adicione uma camada de bolha a um mapa

Este artigo mostra-lhe como renderizar dados de pontos a partir de uma fonte de dados como uma camada de bolha num mapa. As camadas de bolhas tornam os pontos como círculos no mapa com um raio fixo de pixel. 

> [!TIP]
> As camadas de bolhas por padrão tornarão as coordenadas de todas as geometrias numa fonte de dados. Para limitar a camada de tal forma que apenas torna as características de geometria de ponto supor a propriedade `filter` da camada para `['==', ['geometry-type'], 'Point']` ou `['any', ['==', ['geometry-type'], 'Point'], ['==', ['geometry-type'], 'MultiPoint']]` se quiser incluir também as funcionalidades multiPonto.

## <a name="add-a-bubble-layer"></a>Adicionar uma camada de bolha

O código seguinte carrega uma série de pontos numa fonte de dados. Em seguida, liga os pontos de dados a uma [camada de bolha](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.layer.bubblelayer?view=azure-iot-typescript-latest). A camada de bolha torna o raio de cada bolha com cinco pixels e uma cor de enchimento de branco. E, uma cor de traçado de azul, e uma largura de traçado de seis pixels. 

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

Abaixo está a amostra completa de código de execução da funcionalidade acima referida.

<br/>

<iframe height='500' scrolling='no' title='Fonte de dados bubblelayer' src='//codepen.io/azuremaps/embed/mzqaKB/?height=500&theme-id=0&default-tab=js,result&embed-version=2&editable=true' frameborder='no' allowtransparency='true' allowfullscreen='true' style='width: 100%;'>Consulte o <a href='https://codepen.io/azuremaps/pen/mzqaKB/'>Pen BubbleLayer DataSource</a> by Azure Maps<a href='https://codepen.io/azuremaps'> (@azuremaps</a>) no <a href='https://codepen.io'>CodePen</a>.
</iframe>

## <a name="show-labels-with-a-bubble-layer"></a>Mostrar rótulos com uma camada de bolha

Este código mostra-lhe como usar uma camada de bolha para renderum ponto no mapa. E como usar uma camada de símbolo para renderizar uma etiqueta. Para ocultar o ícone da camada de símbolo, desloque a propriedade `image` das opções do ícone para `'none'`.

<br/>

<iframe height='500' scrolling='no' title='MultiLayer DataSource' src='//codepen.io/azuremaps/embed/rqbQXy/?height=500&theme-id=0&default-tab=js,result&embed-version=2&editable=true' frameborder='no' allowtransparency='true' allowfullscreen='true' style='width: 100%;'>Consulte o <a href='https://codepen.io/azuremaps/pen/rqbQXy/'>Pen MultiLayer DataSource</a> by Azure Maps<a href='https://codepen.io/azuremaps'> (@azuremaps</a>) no <a href='https://codepen.io'>CodePen</a>.
</iframe>

## <a name="customize-a-bubble-layer"></a>Personalize uma camada de bolha

A camada bubble tem apenas algumas opções de estilo. Aqui está uma ferramenta para experimentá-los.

<br/>

<iframe height='700' scrolling='no' title='Opções de camada de bolha' src='//codepen.io/azuremaps/embed/eQxbGm/?height=700&theme-id=0&default-tab=result' frameborder='no' allowtransparency='true' allowfullscreen='true' style='width: 100%;'>Consulte as <a href='https://codepen.io/azuremaps/pen/eQxbGm/'>opções</a> de camada de bolha de caneta por Azure Maps<a href='https://codepen.io/azuremaps'> (@azuremaps</a>) no <a href='https://codepen.io'>CodePen</a>.
</iframe>

## <a name="next-steps"></a>Passos seguintes

Saiba mais sobre as aulas e métodos utilizados neste artigo:

> [!div class="nextstepaction"]
> [Camada de Bolhas](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.layer.bubblelayer?view=azure-iot-typescript-latest)

> [!div class="nextstepaction"]
> [Opções de bolhas](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.bubblelayeroptions?view=azure-iot-typescript-latest)

Consulte os seguintes artigos para obter mais amostras de código para adicionar aos seus mapas:

> [!div class="nextstepaction"]
> [Criar uma fonte de dados](create-data-source-web-sdk.md)

> [!div class="nextstepaction"]
> [Adicione uma camada de símbolo](map-add-pin.md)

> [!div class="nextstepaction"]
> [Utilize expressões de estilo baseadas em dados](data-driven-style-expressions-web-sdk.md)

> [!div class="nextstepaction"]
> [Exemplos de código](https://docs.microsoft.com/samples/browse/?products=azure-maps)
