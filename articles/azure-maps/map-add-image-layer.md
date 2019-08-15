---
title: Adicionar uma camada de imagem ao Azure Maps | Microsoft Docs
description: Como adicionar uma camada de imagem ao SDK da Web do Azure Maps.
author: rbrundritt
ms.author: richbrun
ms.date: 07/29/2019
ms.topic: conceptual
ms.service: azure-maps
services: azure-maps
manager: ''
ms.custom: codepen
ms.openlocfilehash: 6c43ccaee473eca701d15a5a83f84814d65c6b7c
ms.sourcegitcommit: 62bd5acd62418518d5991b73a16dca61d7430634
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 08/13/2019
ms.locfileid: "68976150"
---
# <a name="add-an-image-layer-to-a-map"></a>Adicionar uma camada de imagem a um mapa

Este artigo mostra como você pode sobrepor uma imagem ao conjunto fixo de coordenadas no mapa. Há muitos cenários em que a sobreposição de uma imagem no mapa é feita. Aqui estão alguns exemplos do tipo de imagens geralmente sobrepostas em mapas;

* Imagens capturadas de drones.
* Criando floorplans.
* Histórico ou outras imagens de mapa especializadas.
* Plantas de sites de trabalho.
* Imagens de radar de clima.

> [!TIP]
> Um [ImageLayer](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.layer.imagelayer?view=azure-iot-typescript-latest) é uma maneira rápida de sobrepor uma imagem em um mapa. No entanto, se a imagem for grande, o navegador poderá se esforçar para carregá-la. Nesse caso, considere dividir a imagem em blocos e carregá-los no mapa como um [TileLayer](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.layer.tilelayer?view=azure-iot-typescript-latest).

## <a name="add-an-image-layer"></a>Adicionar uma camada de imagem

No código a seguir, sobrepõe uma imagem de um [mapa de Newark New Jersey de 1922](https://www.lib.utexas.edu/maps/historical/newark_nj_1922.jpg) no mapa. Um [ImageLayer](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.layer.imagelayer?view=azure-iot-typescript-latest) é criado passando uma URL para uma imagem e coordenadas para os quatro cantos no formato `[Top Left Corner, Top Right Corner, Bottom Right Corner, Bottom Left Corner]`.

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

Abaixo está o exemplo de código completo em execução da funcionalidade acima.

<br/>

<iframe height='500' scrolling='no' title='Camada de imagem simples' src='//codepen.io/azuremaps/embed/eQodRo/?height=500&theme-id=0&default-tab=js,result&embed-version=2&editable=true' frameborder='no' allowtransparency='true' allowfullscreen='true' style='width: 100%;'>Consulte a <a href='https://codepen.io/azuremaps/pen/eQodRo/'>camada de imagem simples</a> de caneta pelo mapas<a href='https://codepen.io/azuremaps'>@azuremaps</a>do Azure () em <a href='https://codepen.io'>CodePen</a>.
</iframe>

## <a name="import-a-kml-ground-overlay"></a>Importar uma sobreposição de aterramento KML

Este exemplo mostra como sobrepor informações de sobreposição de terra KML como uma camada de imagem no mapa. As sobreposições de aterramento KML fornecem coordenadas norte, Sul, leste e oeste e uma rotação no sentido anti-horário, enquanto a camada de imagem espera coordenadas para cada canto da imagem. A sobreposição de aterramento KML neste exemplo é do Chartres Cathedral e originada de [Wikimedia](https://commons.wikimedia.org/wiki/File:Chartres.svg/overlay.kml).

O código a seguir usa a `getCoordinatesFromEdges` função estática da classe [ImageLayer](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.layer.imagelayer?view=azure-iot-typescript-latest) para calcular os quatro cantos da imagem das informações norte, Sul, leste, oeste e de rotação da sobreposição de aterramento KML.

<br/>

<iframe height='500' scrolling='no' title='Sobreposição de terra KML como camada de imagem' src='//codepen.io/azuremaps/embed/EOJgpj/?height=500&theme-id=0&default-tab=js,result&embed-version=2&editable=true' frameborder='no' allowtransparency='true' allowfullscreen='true' style='width: 100%;'>Confira a caneta KML a sobreposição de <a href='https://codepen.io/azuremaps/pen/EOJgpj/'>aterramento como camada de imagem</a> pelo Azure Maps (<a href='https://codepen.io/azuremaps'>@azuremaps</a>) em <a href='https://codepen.io'>CodePen</a>.
</iframe>

## <a name="customize-an-image-layer"></a>Personalizar uma camada de imagem

A camada de imagem tem muitas opções de estilo. Aqui está uma ferramenta para experimentá-las.

<br/>

<iframe height='700' scrolling='no' title='Opções da camada de imagem' src='//codepen.io/azuremaps/embed/RqOGzx/?height=700&theme-id=0&default-tab=result' frameborder='no' allowtransparency='true' allowfullscreen='true' style='width: 100%;'>Consulte as <a href='https://codepen.io/azuremaps/pen/RqOGzx/'>Opções de camada de imagem</a> de caneta pelo<a href='https://codepen.io/azuremaps'>@azuremaps</a>Azure Maps () em <a href='https://codepen.io'>CodePen</a>.
</iframe>

## <a name="next-steps"></a>Passos Seguintes

Saiba mais sobre as classes e os métodos usados neste artigo:

> [!div class="nextstepaction"]
> [ImageLayer](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.layer.imagelayer?view=azure-iot-typescript-latest)

> [!div class="nextstepaction"]
> [ImageLayerOptions](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.imagelayeroptions?view=azure-iot-typescript-latest)

Consulte os artigos a seguir para obter mais exemplos de código para adicionar aos seus mapas:

> [!div class="nextstepaction"]
> [Adicionar uma camada de peça](./map-add-tile-layer.md)