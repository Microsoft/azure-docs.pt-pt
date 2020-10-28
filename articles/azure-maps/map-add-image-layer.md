---
title: Adicione uma camada de Imagem a um mapa Microsoft Azure Maps
description: Saiba como adicionar imagens a um mapa. Veja como usar o Azure Maps Web SDK para personalizar camadas de imagem e sobrepor imagens em conjuntos fixos de coordenadas.
author: rbrundritt
ms.author: richbrun
ms.date: 07/29/2019
ms.topic: conceptual
ms.service: azure-maps
services: azure-maps
manager: ''
ms.custom: codepen, devx-track-js
ms.openlocfilehash: 43de832095e2c2dd674a156da914ed26f1e472d0
ms.sourcegitcommit: 4064234b1b4be79c411ef677569f29ae73e78731
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/28/2020
ms.locfileid: "92892949"
---
# <a name="add-an-image-layer-to-a-map"></a>Adicione uma camada de imagem a um mapa

Este artigo mostra como sobrepor uma imagem a um conjunto fixo de coordenadas. Aqui estão alguns exemplos de diferentes tipos de imagens que podem ser sobrepostos em mapas:

* Imagens captadas de drones
* Plantações de edifícios
* Imagens históricas ou outras imagens de mapas especializados
* Plantas de locais de trabalho
* Imagens de radar meteorológico

> [!TIP]
> Um [ImageLayer](/javascript/api/azure-maps-control/atlas.layer.imagelayer) é uma maneira fácil de sobrepor uma imagem num mapa. Note que os navegadores podem ter dificuldade em carregar uma grande imagem. Neste caso, considere dividir a sua imagem em azulejos e carregá-las no mapa como um [TileLayer.](/javascript/api/azure-maps-control/atlas.layer.tilelayer)

A camada de imagem suporta os seguintes formatos de imagem:

- JPEG
- PNG
- BMP
- GIF (sem animações)

## <a name="add-an-image-layer"></a>Adicionar uma camada de imagem

O seguinte código sobrepõe uma imagem de um [mapa de Newark, Nova Jersey, de 1922](https://www.lib.utexas.edu/maps/historical/newark_nj_1922.jpg) no mapa. Um [ImageLayer](/javascript/api/azure-maps-control/atlas.layer.imagelayer) é criado passando um URL para uma imagem, e coordena para os quatro cantos no formato `[Top Left Corner, Top Right Corner, Bottom Right Corner, Bottom Left Corner]` .

```javascript
//Create an image layer and add it to the map.
map.layers.add(new atlas.layer.ImageLayer({
    url: 'newark_nj_1922.jpg',
    coordinates: [
        [-74.22655, 40.773941], //Top Left Corner
        [-74.12544, 40.773941], //Top Right Corner
        [-74.12544, 40.712216], //Bottom Right Corner
        [-74.22655, 40.712216]  //Bottom Left Corner
    ]
}));
```

Aqui está a amostra completa do código de execução do código anterior.

<br/>

<iframe height='500' scrolling='no' title='Camada de imagem simples' src='//codepen.io/azuremaps/embed/eQodRo/?height=500&theme-id=0&default-tab=js,result&embed-version=2&editable=true' frameborder='no' loading="lazy" allowtransparency='true' allowfullscreen='true' style='width: 100%;'>Consulte a <a href='https://codepen.io/azuremaps/pen/eQodRo/'>camada de imagem simples pen</a> por Azure Maps <a href='https://codepen.io/azuremaps'>@azuremaps</a> () no <a href='https://codepen.io'>CodePen</a>.
</iframe>

## <a name="import-a-kml-file-as-ground-overlay"></a>Importar um ficheiro KML como sobreposição de solo

Esta amostra demonstra como adicionar informações sobrepostas ao solo KML como uma camada de imagem no mapa. As sobreposições de terra kml fornecem coordenadas norte, sul, leste e oeste, e uma rotação no sentido contrário ao dos ponteiros do relógio. Mas, a camada de imagem espera coordenadas para cada canto da imagem. A sobreposição de terra KML nesta amostra é para a catedral de Chartres, e é proveniente da [Wikimedia.](https://commons.wikimedia.org/wiki/File:Chartres.svg/overlay.kml)

O código utiliza a função estática `getCoordinatesFromEdges` da classe [ImageLayer.](/javascript/api/azure-maps-control/atlas.layer.imagelayer) Calcula os quatro cantos da imagem usando a informação de norte, sul, leste, oeste e rotação da sobreposição do solo KML.

<br/>

<iframe height='500' scrolling='no' title='Sobreposição de solo KML como camada de imagem' src='//codepen.io/azuremaps/embed/EOJgpj/?height=500&theme-id=0&default-tab=js,result&embed-version=2&editable=true' frameborder='no' loading="lazy" allowtransparency='true' allowfullscreen='true' style='width: 100%;'>Consulte a sobreposição do solo de Pen <a href='https://codepen.io/azuremaps/pen/EOJgpj/'>KML como Camada de Imagem</a> por Azure Maps ( ) em <a href='https://codepen.io/azuremaps'>@azuremaps</a> <a href='https://codepen.io'>CodePen</a>.
</iframe>

## <a name="customize-an-image-layer"></a>Personalize uma camada de imagem

A camada de imagem tem muitas opções de estilo. Aqui está uma ferramenta para experimentá-los.

<br/>

<iframe height='700' scrolling='no' title='Opções de camada de imagem' src='//codepen.io/azuremaps/embed/RqOGzx/?height=700&theme-id=0&default-tab=result' frameborder='no' loading="lazy" allowtransparency='true' allowfullscreen='true' style='width: 100%;'>Consulte as <a href='https://codepen.io/azuremaps/pen/RqOGzx/'>opções de camada de imagem de caneta</a> por Azure Maps ( ) em <a href='https://codepen.io/azuremaps'>@azuremaps</a> <a href='https://codepen.io'>CodePen</a>.
</iframe>

## <a name="next-steps"></a>Passos seguintes

Saiba mais sobre as aulas e métodos utilizados neste artigo:

> [!div class="nextstepaction"]
> [ImageLayer](/javascript/api/azure-maps-control/atlas.layer.imagelayer)

> [!div class="nextstepaction"]
> [Opções ImageLayer](/javascript/api/azure-maps-control/atlas.imagelayeroptions)

Consulte os seguintes artigos para obter mais amostras de código para adicionar aos seus mapas:

> [!div class="nextstepaction"]
> [Adicionar uma camada de mosaico](./map-add-tile-layer.md)