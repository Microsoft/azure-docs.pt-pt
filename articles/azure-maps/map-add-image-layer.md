---
title: Adicione uma camada de imagem a um mapa Microsoft Azure Maps
description: Neste artigo, você aprenderá sobre como sobrepor uma imagem num mapa usando o Microsoft Azure Maps Web SDK.
author: rbrundritt
ms.author: richbrun
ms.date: 07/29/2019
ms.topic: conceptual
ms.service: azure-maps
services: azure-maps
manager: ''
ms.custom: codepen
ms.openlocfilehash: 69bf41f9d88081b9a416b9bee91e8650a84f12c7
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/28/2020
ms.locfileid: "77209720"
---
# <a name="add-an-image-layer-to-a-map"></a>Adicione uma camada de imagem a um mapa

Este artigo mostra-lhe como sobrepor uma imagem a um conjunto fixo de coordenadas. Aqui estão alguns exemplos de diferentes tipos de imagens que podem ser sobrepostos em mapas:

* Imagens captadas a partir de drones
* Planos de construção
* Imagens históricas ou outras imagens de mapas especializados
* Plantas de locais de trabalho
* Imagens de radar estoma do tempo

> [!TIP]
> Uma [ImageLayer](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.layer.imagelayer?view=azure-iot-typescript-latest) é uma maneira fácil de sobrepor uma imagem num mapa. Note que os navegadores podem ter dificuldade em carregar uma grande imagem. Neste caso, considere dividir a sua imagem em azulejos e carregá-los no mapa como [TileLayer](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.layer.tilelayer?view=azure-iot-typescript-latest).

A camada de imagem suporta os seguintes formatos de imagem:

- JPEG
- PNG
- BMP
- GIF (sem animações)

## <a name="add-an-image-layer"></a>Adicionar uma camada de imagem

O código que se segue sobrepõe uma imagem de um [mapa de Newark, Nova Jersey, de 1922](https://www.lib.utexas.edu/maps/historical/newark_nj_1922.jpg) no mapa. Um [ImageLayer](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.layer.imagelayer?view=azure-iot-typescript-latest) é criado passando um URL para uma imagem, `[Top Left Corner, Top Right Corner, Bottom Right Corner, Bottom Left Corner]`e coordena para os quatro cantos no formato .

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

<iframe height='500' scrolling='no' title='Camada de imagem simples' src='//codepen.io/azuremaps/embed/eQodRo/?height=500&theme-id=0&default-tab=js,result&embed-version=2&editable=true' frameborder='no' allowtransparency='true' allowfullscreen='true' style='width: 100%;'>Consulte a camada de <a href='https://codepen.io/azuremaps/pen/eQodRo/'>imagem</a> simples<a href='https://codepen.io/azuremaps'>@azuremaps</a>pen por Azure Maps () em <a href='https://codepen.io'>CodePen</a>.
</iframe>

## <a name="import-a-kml-file-as-ground-overlay"></a>Importar um ficheiro KML como sobreposição de solo

Esta amostra demonstra como adicionar informação sobreposta de terra KML como uma camada de imagem no mapa. As sobreposições terrestres kml fornecem coordenadas norte, sul, leste e oeste, e uma rotação no sentido contrário ao dos ponteiros do relógio. Mas, a camada de imagem espera coordenadas para cada canto da imagem. A sobreposição do solo KML nesta amostra é para a catedral de Chartres, e é proveniente da [Wikimedia.](https://commons.wikimedia.org/wiki/File:Chartres.svg/overlay.kml)

O código utiliza `getCoordinatesFromEdges` a função estática da classe [ImageLayer.](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.layer.imagelayer?view=azure-iot-typescript-latest) Calcula os quatro cantos da imagem utilizando a informação de norte, sul, leste, oeste e rotação da sobreposição do solo KML.

<br/>

<iframe height='500' scrolling='no' title='KmL Ground Overlay como Camada de Imagem' src='//codepen.io/azuremaps/embed/EOJgpj/?height=500&theme-id=0&default-tab=js,result&embed-version=2&editable=true' frameborder='no' allowtransparency='true' allowfullscreen='true' style='width: 100%;'>Veja a sobreposição do <a href='https://codepen.io/azuremaps/pen/EOJgpj/'>solo pen KML como Camada de Imagem</a> por Azure Maps ()<a href='https://codepen.io/azuremaps'>@azuremaps</a>em <a href='https://codepen.io'>CodePen</a>.
</iframe>

## <a name="customize-an-image-layer"></a>Personalize uma camada de imagem

A camada de imagem tem muitas opções de estilo. Aqui está uma ferramenta para experimentá-los.

<br/>

<iframe height='700' scrolling='no' title='Opções de camada de imagem' src='//codepen.io/azuremaps/embed/RqOGzx/?height=700&theme-id=0&default-tab=result' frameborder='no' allowtransparency='true' allowfullscreen='true' style='width: 100%;'>Consulte as <a href='https://codepen.io/azuremaps/pen/RqOGzx/'>opções</a> de camada<a href='https://codepen.io/azuremaps'>@azuremaps</a>de imagem da caneta por Azure Maps () no <a href='https://codepen.io'>CodePen</a>.
</iframe>

## <a name="next-steps"></a>Passos seguintes

Saiba mais sobre as aulas e métodos utilizados neste artigo:

> [!div class="nextstepaction"]
> [Camada de imagem](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.layer.imagelayer?view=azure-iot-typescript-latest)

> [!div class="nextstepaction"]
> [Opções de Camadas de Imagem](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.imagelayeroptions?view=azure-iot-typescript-latest)

Consulte os seguintes artigos para obter mais amostras de código para adicionar aos seus mapas:

> [!div class="nextstepaction"]
> [Adicionar uma camada de mosaico](./map-add-tile-layer.md)