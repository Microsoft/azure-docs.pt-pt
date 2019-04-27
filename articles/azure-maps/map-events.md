---
title: Processar eventos de mouse com o Azure Maps | Documentos da Microsoft
description: Como tornar um mapa interativo do Javascript com eventos de mapa
author: jingjing-z
ms.author: jinzh
ms.date: 11/29/2018
ms.topic: conceptual
ms.service: azure-maps
services: azure-maps
manager: ''
ms.custom: codepen
ms.openlocfilehash: 4fce8eae25942d098bb3f3277938bfaa3dafa00b
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/23/2019
ms.locfileid: "60770453"
---
# <a name="interact-with-the-map---mouse-events"></a>Interagir com o mapa - eventos de mouse

Este artigo mostra-lhe como utilizar [mapear a classe](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.map?view=azure-iot-typescript-latest) [eventos](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.map?view=azure-iot-typescript-latest) propriedade para realçar os eventos no mapa e em diferentes camadas do mapa. Ele também mostra como utilizar a propriedade de eventos da classe de mapa para realçar os eventos quando interage com um marcador HTML.

## <a name="interact-with-the-map"></a>Interagir com o mapa

<iframe height='600' scrolling='no' title='Interagir com o mapa – eventos de mouse' src='//codepen.io/azuremaps/embed/bLZEWd/?height=600&theme-id=0&default-tab=js,result&embed-version=2&editable=true' frameborder='no' allowtransparency='true' allowfullscreen='true' style='width: 100%;'>Consulte a caneta <a href='https://codepen.io/azuremaps/pen/bLZEWd/'>interagir com o mapa – eventos de mouse</a> ao Azure Maps (<a href='https://codepen.io/azuremaps'>@azuremaps</a>) na <a href='https://codepen.io'>CodePen</a>.
</iframe>

Brincar com o mapa acima e ver os eventos de mouse correspondente realçados no lado direito. Pode clicar no **separador JS** para ver e editar o código JavaScript. Também pode clicar no **editar no CodePen** botão e editar o código no CodePen.

## <a name="interact-with-map-layers"></a>Interagir com camadas de mapa

<iframe height='600' scrolling='no' title='Interagir com o mapa – eventos de camada' src='//codepen.io/azuremaps/embed/bQRRPE/?height=600&theme-id=0&default-tab=js,result&embed-version=2&editable=true' frameborder='no' allowtransparency='true' allowfullscreen='true' style='width: 100%;'>Consulte a caneta <a href='https://codepen.io/azuremaps/pen/bQRRPE/'>interagir com o mapa – eventos de camada</a> ao Azure Maps (<a href='https://codepen.io/azuremaps'>@azuremaps</a>) na <a href='https://codepen.io'>CodePen</a>.
</iframe>

O código acima destaca o nome dos eventos que obter acionados à medida que interage com a camada de símbolo. A camada de símbolo, bolhas, linha e polígono todas suportam o mesmo conjunto de eventos. A camada de mosaico não suporta qualquer um desses eventos.

## <a name="interact-with-html-marker"></a>Interagir com HTML marcador

<iframe height='500' scrolling='no' title='Interagir com o mapa - eventos de marcador de HTML' src='//codepen.io/azuremaps/embed/VVzKJY/?height=500&theme-id=0&default-tab=js,result&embed-version=2&editable=true' frameborder='no' allowtransparency='true' allowfullscreen='true' style='width: 100%;'>Consulte a caneta <a href='https://codepen.io/azuremaps/pen/VVzKJY/'>interagir com o mapa - eventos de marcador de HTML</a> ao Azure Maps (<a href='https://codepen.io/azuremaps'>@azuremaps</a>) na <a href='https://codepen.io'>CodePen</a>.
</iframe>

O código acima adiciona eventos de mapa de Javascript para um marcador HTML. Além disso, destaca o nome dos eventos que obter acionados à medida que interage com o marcador HTML.

## <a name="next-steps"></a>Passos Seguintes

Saiba mais sobre as classes e métodos usados neste artigo:

> [!div class="nextstepaction"]
> [Mapa](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.map?view=azure-iot-typescript-latest)

Veja os artigos seguintes para obter exemplos de código completo:

> [!div class="nextstepaction"]
> [Utilizar o módulo de serviços de mapas do Azure](./how-to-use-services-module.md)

> [!div class="nextstepaction"]
> [Página de exemplo de código](https://aka.ms/AzureMapsSamples)
