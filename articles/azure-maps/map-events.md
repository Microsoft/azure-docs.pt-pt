---
title: Manipular eventos de mouse com mapas do Azure | Microsoft Docs
description: Como criar um mapa de SDK da Web interativo com eventos de mapa
author: jingjing-z
ms.author: jinzh
ms.date: 07/29/2019
ms.topic: conceptual
ms.service: azure-maps
services: azure-maps
manager: ''
ms.custom: codepen
ms.openlocfilehash: 37a3fc3178fe5caeacedfd355a6065ee189a5890
ms.sourcegitcommit: 62bd5acd62418518d5991b73a16dca61d7430634
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 08/13/2019
ms.locfileid: "68976536"
---
# <a name="interact-with-the-map---mouse-events"></a>Interagir com os eventos MAP-mouse

Este artigo mostra como usar a propriedade de [eventos da classe Map](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.map?#events) para realçar eventos no mapa e em camadas diferentes do mapa. Ele também mostra como usar a propriedade de eventos da classe Map para realçar eventos quando você interage com um marcador HTML.

## <a name="interact-with-the-map"></a>Interagir com o mapa

Jogue com o mapa abaixo e veja os eventos de mouse correspondentes realçados à direita. Você pode clicar na **guia js** para exibir e editar o código JavaScript. Você também pode clicar no botão **Editar no CodePen** e editar o código em CodePen.

<br/>

<iframe height='600' scrolling='no' title='Interagindo com o mapa – eventos de mouse' src='//codepen.io/azuremaps/embed/bLZEWd/?height=600&theme-id=0&default-tab=js,result&embed-version=2&editable=true' frameborder='no' allowtransparency='true' allowfullscreen='true' style='width: 100%;'>Consulte a caneta <a href='https://codepen.io/azuremaps/pen/bLZEWd/'>interagir com o mapa – eventos de mouse</a> por mapas do<a href='https://codepen.io/azuremaps'>@azuremaps</a>Azure () em <a href='https://codepen.io'>CodePen</a>.
</iframe>

## <a name="interact-with-map-layers"></a>Interagir com as camadas do mapa

O código a seguir realça o nome dos eventos que são acionados enquanto você interage com a camada de símbolo. O símbolo, a bolha, a linha e a camada de polígono oferecem suporte ao mesmo conjunto de eventos. As camadas mapa de calor e bloco não oferecem suporte a nenhum desses eventos.

<br/>

<iframe height='600' scrolling='no' title='Interagindo com o mapa – eventos de camada' src='//codepen.io/azuremaps/embed/bQRRPE/?height=600&theme-id=0&default-tab=js,result&embed-version=2&editable=true' frameborder='no' allowtransparency='true' allowfullscreen='true' style='width: 100%;'>Consulte a caneta <a href='https://codepen.io/azuremaps/pen/bQRRPE/'>interagindo com os eventos de camada de mapa</a> por mapas do<a href='https://codepen.io/azuremaps'>@azuremaps</a>Azure () em <a href='https://codepen.io'>CodePen</a>.
</iframe>

## <a name="interact-with-html-marker"></a>Interagir com marcador HTML

O código a seguir adiciona eventos de mapa JavaScript a um marcador HTML. Ele também realça o nome dos eventos que são acionados enquanto você interage com o marcador HTML.

<br/>

<iframe height='500' scrolling='no' title='Interagindo com o mapa-eventos de marcador HTML' src='//codepen.io/azuremaps/embed/VVzKJY/?height=500&theme-id=0&default-tab=js,result&embed-version=2&editable=true' frameborder='no' allowtransparency='true' allowfullscreen='true' style='width: 100%;'>Consulte a caneta <a href='https://codepen.io/azuremaps/pen/VVzKJY/'>interagindo com os eventos do marcador MAP-HTML</a> pelo Azure Maps<a href='https://codepen.io/azuremaps'>@azuremaps</a>() em <a href='https://codepen.io'>CodePen</a>.
</iframe>

## <a name="next-steps"></a>Passos Seguintes

Consulte os seguintes artigos para obter exemplos de código completo:

> [!div class="nextstepaction"]
> [Usando o módulo de serviços do Azure Maps](./how-to-use-services-module.md)

> [!div class="nextstepaction"]
> [Exemplos de código](https://docs.microsoft.com/samples/browse/?products=azure-maps)
