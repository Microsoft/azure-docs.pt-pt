---
title: Definir opções de desenho no Azure Maps | Microsoft Docs
description: Como definir dados de opções de desenho usando o SDK da Web do Azure Maps
author: walsehgal
ms.author: v-musehg
ms.date: 09/04/2019
ms.topic: conceptual
ms.service: azure-maps
services: azure-maps
manager: philmea
ms.openlocfilehash: d85525274db7818737b62ad4e9ea2026b8aef3b2
ms.sourcegitcommit: f176e5bb926476ec8f9e2a2829bda48d510fbed7
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 09/04/2019
ms.locfileid: "70309799"
---
# <a name="set-drawing-options"></a>Definir opções de desenho

Este artigo mostra como as diferentes opções do [Gerenciador de desenho](https://docs.microsoft.com/javascript/api/azure-maps-drawing-tools/atlas.drawing.drawingmanager?view=azure-node-latest#setoptions-drawingmanageroptions-) alteram a experiência do usuário. Você pode especificar opções para o Gerenciador de desenho ao criar uma instância dele ou usar a função **drawingmanager. SetOptions ()** como alternativa para definir opções.


## <a name="set-drawing-mode"></a>Definir modo de desenho

O código a seguir cria uma instância do Gerenciador de desenho e define a opção de **modo** de desenho. 

```Javascript
//Create an instance of the drawing manager and set drawing mode.
drawingManager = new atlas.drawing.DrawingManager(map,{
    mode: "draw-polygon"
});
```

O código a seguir é um exemplo de execução completa de como definir um modo de desenho do Gerenciador de desenho. Clique no mapa para começar a desenhar um polígono.

<br/>

<iframe height="500" style="width: 100%;" scrolling="no" title="Desenhar um polígono" src="//codepen.io/azuremaps/embed/YzKVKRa/?height=265&theme-id=0&default-tab=js,result&editable=true" frameborder="no" allowtransparency="true" allowfullscreen="true">
Consulte a caneta <a href='https://codepen.io/azuremaps/pen/YzKVKRa/'>desenhar um polígono</a> pelo Azure Maps (<a href='https://codepen.io/azuremaps'>@azuremaps</a>) em <a href='https://codepen.io'>CodePen</a>.
</iframe>


## <a name="set-interaction-type"></a>Definir tipo de interação

O código a seguir define o tipo de interação de desenho para a qual o Gerenciador de desenho deve aderir. 

```Javascript
//Create an instance of the drawing manager and set drawing mode.
drawingManager = new atlas.drawing.DrawingManager(map,{
    mode: "draw-polygon",
    interactionType: "freehand"
});
```

Veja abaixo o exemplo de código que implementa a funcionalidade que permite desenhar no mapa livremente, mantendo pressionado o botão esquerdo do mouse e arrastando-o. 

<br/>

<iframe height="500" style="width: 100%;" scrolling="no" title="Desenho livre" src="//codepen.io/azuremaps/embed/ZEzKoaj/?height=265&theme-id=0&default-tab=js,result&editable=true" frameborder="no" allowtransparency="true" allowfullscreen="true">
Consulte o desenho de caneta <a href='https://codepen.io/azuremaps/pen/ZEzKoaj/'>livre</a> pelo mapas do Azure (<a href='https://codepen.io/azuremaps'>@azuremaps</a>) em <a href='https://codepen.io'>CodePen</a>.
</iframe>


## <a name="customizing-drawing-options"></a>Personalizando opções de desenho

Os exemplos anteriores demonstraram como personalizar opções de desenho ao instanciar o Gerenciador de desenho. Você também pode definir as opções do Gerenciador de desenho usando a função **drawingprovider. SetOptions ()** . Abaixo está uma ferramenta para testar a personalização de todas as opções do Gerenciador de desenho usando a função SetOptions.

<br/>

<iframe height="685" title="Personalizar o Gerenciador de desenho" src="//codepen.io/azuremaps/embed/LYPyrxR/?height=600&theme-id=0&default-tab=result" frameborder="no" allowtransparency="true" allowfullscreen="true" style='width: 100%;'>Consulte a caneta <a href='https://codepen.io/azuremaps/pen/LYPyrxR/'>obter dados de forma</a> por mapas do<a href='https://codepen.io/azuremaps'>@azuremaps</a>Azure () em <a href='https://codepen.io'>CodePen</a>.
</iframe>


## <a name="next-steps"></a>Passos Seguintes

Saiba mais sobre as classes e os métodos usados neste artigo:

> [!div class="nextstepaction"]
> [Mapeada](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.map?view=azure-iot-typescript-latest)

> [!div class="nextstepaction"]
> [Gerenciador de desenho](https://docs.microsoft.com/javascript/api/azure-maps-drawing-tools/atlas.drawing.drawingmanager?view=azure-node-latest)

> [!div class="nextstepaction"]
> [Barra de ferramentas desenho](https://docs.microsoft.com/javascript/api/azure-maps-drawing-tools/atlas.control.drawingtoolbar?view=azure-node-latest)
