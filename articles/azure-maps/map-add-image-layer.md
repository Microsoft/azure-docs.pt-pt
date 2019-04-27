---
title: Adicionar uma camada de imagem para o Azure Maps | Documentos da Microsoft
description: Como adicionar uma camada de imagem para o mapa de Javascript
author: rbrundritt
ms.author: richbrun
ms.date: 12/3/2018
ms.topic: conceptual
ms.service: azure-maps
services: azure-maps
manager: ''
ms.custom: codepen
ms.openlocfilehash: 5396fefca3a60dea7a503f8b4e84cc575753ea30
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/23/2019
ms.locfileid: "60769571"
---
# <a name="add-an-image-layer-to-a-map"></a>Adicionar uma camada de imagem para um mapa

Este artigo mostra como pode sobrepor uma imagem a conjunto fixo de coordenadas do mapa. Existem muitos cenários em que a sobreposição de uma imagem do mapa é feito. Aqui estão alguns exemplos dos tipos de imagens, muitas vezes, sobreposto maps;

* Imagens a partir de drones.
* Floorplans de construção.
* Históricos ou outras imagens de mapa especializados.
* Esquemas de locais de trabalho.
* Imagens de planos de Meteorologia.

> [!TIP]
> Uma [ImageLayer](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.layer.imagelayer?view=azure-iot-typescript-latest) é uma forma fácil de colocar uma imagem num mapa de uma rápida. No entanto, se a imagem for grande, o navegador pode apresentar dificuldades para carregá-lo. Neste caso, considere dividi sua imagem em mosaicos e carregando-os em mapa como um [TileLayer](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.layer.tilelayer?view=azure-iot-typescript-latest).

## <a name="add-an-image-layer"></a>Adicionar uma camada de imagem

Este exemplo mostra como sobrepor uma imagem de um [mapa de Newark Nova Jérsia de 1922](https://www.lib.utexas.edu/maps/historical/newark_nj_1922.jpg) no mapa.

<br/>

<iframe height='500' scrolling='no' title='Camada de imagem simples' src='//codepen.io/azuremaps/embed/eQodRo/?height=500&theme-id=0&default-tab=js,result&embed-version=2&editable=true' frameborder='no' allowtransparency='true' allowfullscreen='true' style='width: 100%;'>Consulte a caneta <a href='https://codepen.io/azuremaps/pen/eQodRo/'>camada de imagem simples</a> através do Azure Maps (<a href='https://codepen.io/azuremaps'>@azuremaps</a>) na <a href='https://codepen.io'>CodePen</a>.
</iframe>

No código acima, o primeiro bloco de código constrói um objeto de mapa. Pode ver [criar um mapa](./map-create.md) para obter instruções.

No segundo bloco de código, um [ImageLayer](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.layer.imagelayer?view=azure-iot-typescript-latest) é criado pela transmissão de um URL para uma imagem e coordenadas para os quatro cantos no formato `[Top Left Corner, Top Right Corner, Bottom Right Corner, Bottom Left Corner]`.

## <a name="import-a-kml-ground-overlay"></a>Importar uma sobreposição de zero KML

Este exemplo mostra como sobrepor as informações de sobreposição de zero KML como uma camada de imagem no mapa. Sobreposições de zero KML fornecem norte, south, leste e coordenadas oeste e uma rotação anti-horário e onde como imagem de camada de espera coordenadas para cada canto da imagem. A sobreposição de zero KML neste exemplo é do Chartres cathedral e fonte aberto da [Wikimedia](https://commons.wikimedia.org/wiki/File:Chartres.svg/overlay.kml).

<br/>

<iframe height='500' scrolling='no' title='Sobreposição de zero KML como camada de imagem' src='//codepen.io/azuremaps/embed/EOJgpj/?height=500&theme-id=0&default-tab=js,result&embed-version=2&editable=true' frameborder='no' allowtransparency='true' allowfullscreen='true' style='width: 100%;'>Consulte a caneta <a href='https://codepen.io/azuremaps/pen/EOJgpj/'>KML sobreposição como camada de imagem de fundo</a> através do Azure Maps (<a href='https://codepen.io/azuremaps'>@azuremaps</a>) na <a href='https://codepen.io'>CodePen</a>.
</iframe>

O código anterior usa a estática `getCoordinatesFromEdges` função do [ImageLayer](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.layer.imagelayer?view=azure-iot-typescript-latest) de classe para calcular os quatro cantos da imagem a partir do Norte, south, leste, oeste e rotação as informações do KML a partir do zero sobreposição.


## <a name="customize-an-image-layer"></a>Personalizar uma camada de imagem

A camada de imagem tem muitas opções de estilo. Aqui está uma ferramenta de experimentá-las.

<br/>

<iframe height='700' scrolling='no' title='Opções de camada de imagem' src='//codepen.io/azuremaps/embed/RqOGzx/?height=700&theme-id=0&default-tab=result' frameborder='no' allowtransparency='true' allowfullscreen='true' style='width: 100%;'>Consulte a caneta <a href='https://codepen.io/azuremaps/pen/RqOGzx/'>opções de camada de imagem</a> através do Azure Maps (<a href='https://codepen.io/azuremaps'>@azuremaps</a>) na <a href='https://codepen.io'>CodePen</a>.
</iframe>

## <a name="next-steps"></a>Passos Seguintes

Saiba mais sobre as classes e métodos usados neste artigo:

> [!div class="nextstepaction"]
> [ImageLayer](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.layer.imagelayer?view=azure-iot-typescript-latest)

> [!div class="nextstepaction"]
> [ImageLayerOptions](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.imagelayeroptions?view=azure-iot-typescript-latest)

Veja os artigos seguintes para obter mais amostras de código adicionar a seus mapas:

> [!div class="nextstepaction"]
> [Adicionar uma camada de mosaico](./map-add-tile-layer.md)