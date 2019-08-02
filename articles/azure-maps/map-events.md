---
title: Manipular eventos de mouse com mapas do Azure | Microsoft Docs
description: Como fazer um mapa JavaScript interativo com eventos de mapa
author: jingjing-z
ms.author: jinzh
ms.date: 07/29/2019
ms.topic: conceptual
ms.service: azure-maps
services: azure-maps
manager: ''
ms.custom: codepen
ms.openlocfilehash: da6b183155de0fbc370751254a6842343d280874
ms.sourcegitcommit: 3877b77e7daae26a5b367a5097b19934eb136350
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 07/30/2019
ms.locfileid: "68638965"
---
# <a name="interact-with-the-map---mouse-events"></a>Interagir com os eventos MAP-mouse

Este artigo mostra como usar a propriedade de [eventos](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.map?view=azure-iot-typescript-latest) da [classe Map](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.map?view=azure-iot-typescript-latest) para realçar eventos no mapa e em camadas diferentes do mapa. Ele também mostra como usar a propriedade de eventos da classe Map para realçar eventos quando você interage com um marcador HTML.

## <a name="interact-with-the-map"></a>Interagir com o mapa

<iframe height='600' scrolling='no' title='Interagindo com o mapa – eventos de mouse' src='//codepen.io/azuremaps/embed/bLZEWd/?height=600&theme-id=0&default-tab=js,result&embed-version=2&editable=true' frameborder='no' allowtransparency='true' allowfullscreen='true' style='width: 100%;'>Consulte a caneta <a href='https://codepen.io/azuremaps/pen/bLZEWd/'>interagir com o mapa – eventos de mouse</a> por mapas do<a href='https://codepen.io/azuremaps'>@azuremaps</a>Azure () em <a href='https://codepen.io'>CodePen</a>.
</iframe>

Jogue com o mapa acima e veja os eventos de mouse correspondentes realçados à direita. Você pode clicar na **guia js** para exibir e editar o código JavaScript. Você também pode clicar no botão **Editar no CodePen** e editar o código em CodePen.

## <a name="interact-with-map-layers"></a>Interagir com as camadas do mapa

<iframe height='600' scrolling='no' title='Interagindo com o mapa – eventos de camada' src='//codepen.io/azuremaps/embed/bQRRPE/?height=600&theme-id=0&default-tab=js,result&embed-version=2&editable=true' frameborder='no' allowtransparency='true' allowfullscreen='true' style='width: 100%;'>Consulte a caneta <a href='https://codepen.io/azuremaps/pen/bQRRPE/'>interagindo com os eventos de camada de mapa</a> por mapas do<a href='https://codepen.io/azuremaps'>@azuremaps</a>Azure () em <a href='https://codepen.io'>CodePen</a>.
</iframe>

O código acima realça o nome dos eventos que são acionados enquanto você interage com a camada de símbolo. O símbolo, a bolha, a linha e a camada de polígono oferecem suporte ao mesmo conjunto de eventos. A camada de peça não oferece suporte a nenhum desses eventos.

## <a name="interact-with-html-marker"></a>Interagir com marcador HTML

<iframe height='500' scrolling='no' title='Interagindo com o mapa-eventos de marcador HTML' src='//codepen.io/azuremaps/embed/VVzKJY/?height=500&theme-id=0&default-tab=js,result&embed-version=2&editable=true' frameborder='no' allowtransparency='true' allowfullscreen='true' style='width: 100%;'>Consulte a caneta <a href='https://codepen.io/azuremaps/pen/VVzKJY/'>interagindo com os eventos do marcador MAP-HTML</a> pelo Azure Maps<a href='https://codepen.io/azuremaps'>@azuremaps</a>() em <a href='https://codepen.io'>CodePen</a>.
</iframe>

O código acima adiciona eventos de mapa JavaScript a um marcador HTML. Ele também realça o nome dos eventos que são acionados enquanto você interage com o marcador HTML.

## <a name="next-steps"></a>Passos Seguintes

Saiba mais sobre as classes e os métodos usados neste artigo:

> [!div class="nextstepaction"]
> [Mapeada](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.map?view=azure-iot-typescript-latest)

Consulte os seguintes artigos para obter exemplos de código completo:

> [!div class="nextstepaction"]
> [Usando o módulo de serviços do Azure Maps](./how-to-use-services-module.md)

> [!div class="nextstepaction"]
> [Página de exemplo de código](https://aka.ms/AzureMapsSamples)
