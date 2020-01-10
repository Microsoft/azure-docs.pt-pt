---
title: Adicionar uma barra de ferramentas de desenho ao Azure Maps | Microsoft Docs
description: Como adicionar uma barra de ferramentas de desenho a um mapa usando o SDK da Web do Azure Maps
author: walsehgal
ms.author: v-musehg
ms.date: 09/04/2019
ms.topic: conceptual
ms.service: azure-maps
services: azure-maps
manager: philmea
ms.openlocfilehash: ed16d8797e541ee474b4f52f3d4379721922a734
ms.sourcegitcommit: f4f626d6e92174086c530ed9bf3ccbe058639081
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 12/25/2019
ms.locfileid: "75432875"
---
# <a name="add-a-drawing-tools-toolbar-to-a-map"></a>Adicionar uma barra de ferramentas de ferramentas de desenho a um mapa

Este artigo mostra como usar o módulo ferramentas de desenho e exibir a barra de ferramentas desenho no mapa. O controle [DrawingToolbar](https://docs.microsoft.com/javascript/api/azure-maps-drawing-tools/atlas.control.drawingtoolbar?view=azure-node-latest) adiciona a barra de ferramentas de desenho no mapa. Você aprenderá a criar mapas com apenas uma e todas as ferramentas de desenho e como personalizar a renderização das formas de desenho no Gerenciador de desenho.

## <a name="add-drawing-toolbar"></a>Adicionar barra de ferramentas de desenho

O código a seguir cria uma instância do Gerenciador de desenho e exibe a barra de ferramentas no mapa.

```Javascript
//Create an instance of the drawing manager and display the drawing toolbar.
drawingManager = new atlas.drawing.DrawingManager(map, {
        toolbar: new atlas.control.DrawingToolbar({
            position: 'top-right',
            style: 'dark'
        })
    });
```

Abaixo está o exemplo de código completo em execução da funcionalidade acima:

<br/>

<iframe height="500" style="width: 100%;" scrolling="no" title="Adicionar barra de ferramentas de desenho" src="//codepen.io/azuremaps/embed/ZEzLeRg/?height=265&theme-id=0&default-tab=js,result&editable=true" frameborder="no" allowtransparency="true" allowfullscreen="true">
Consulte a <a href='https://codepen.io/azuremaps/pen/ZEzLeRg/'>barra de ferramentas Adicionar desenho</a> da caneta pelo mapas do Azure (<a href='https://codepen.io/azuremaps'>@azuremaps</a>) em <a href='https://codepen.io'>CodePen</a>.
</iframe>


## <a name="limit-displayed-toolbar-options"></a>Opções de barra de ferramentas de limite exibidas

O código a seguir cria uma instância do Gerenciador de desenho e exibe a barra de ferramentas com apenas uma ferramenta de desenho de polígono no mapa. 

```Javascript
//Create an instance of the drawing manager and display the drawing toolbar with polygon drawing tool.
drawingManager = new atlas.drawing.DrawingManager(map, {
        toolbar: new atlas.control.DrawingToolbar({
            position: 'top-right',
            style: 'light',
            buttons: ["draw-polygon"]
        })
    });
```

Abaixo está o exemplo de código completo em execução da funcionalidade acima:

<br/>

<iframe height="500" style="width: 100%;" scrolling="no" title="Adicionar uma ferramenta de desenho de polígono" src="//codepen.io/azuremaps/embed/OJLWWMy/?height=265&theme-id=0&default-tab=js,result&editable=true" frameborder="no" allowtransparency="true" allowfullscreen="true">
Consulte a caneta <a href='https://codepen.io/azuremaps/pen/OJLWWMy/'>Adicionar uma ferramenta de desenho de polígono</a> pelo Azure Maps (<a href='https://codepen.io/azuremaps'>@azuremaps</a>) em <a href='https://codepen.io'>CodePen</a>.
</iframe>


## <a name="change-drawing-rendering-style"></a>Alterar o estilo de renderização do desenho

O código a seguir obtém as camadas de renderização do Gerenciador de desenho e modifica suas opções para alterar o estilo de renderização para desenho. Nesse caso, os pontos serão renderizados com um ícone de marcador azul, as linhas serão vermelhas e quatro pixels de largura, polígonos terão uma cor de preenchimento verde e uma estrutura de tópicos laranja.

```Javascript
var layers = drawingManager.getLayers();
    layers.pointLayer.setOptions({
        iconOptions: {
            image: 'marker-blue'
        }
    });
    layers.lineLayer.setOptions({
        strokeColor: 'red',
        strokeWidth: 4
    });
    layers.polygonLayer.setOptions({
        fillColor: 'green'
    });
    layers.polygonOutlineLayer.setOptions({
        strokeColor: 'orange'
    });
```

Abaixo está o exemplo de código completo em execução da funcionalidade acima:

<br/>

<iframe height="500" style="width: 100%;" scrolling="no" title="Alterar o estilo de renderização do desenho" src="//codepen.io/azuremaps/embed/OJLWpyj/?height=265&theme-id=0&default-tab=js,result&editable=true" frameborder="no" allowtransparency="true" allowfullscreen="true">
Consulte o <a href='https://codepen.io/azuremaps/pen/OJLWpyj/'>estilo de renderização de desenho de alteração</a> de caneta pelo mapas do Azure (<a href='https://codepen.io/azuremaps'>@azuremaps</a>) em <a href='https://codepen.io'>CodePen</a>.
</iframe>


## <a name="next-steps"></a>Passos seguintes

Saiba como usar recursos adicionais do módulo ferramentas de desenho:

> [!div class="nextstepaction"]
> [Obter dados da forma](map-get-shape-data.md)

> [!div class="nextstepaction"]
> [Reagir a eventos de desenho](drawing-tools-events.md)

> [!div class="nextstepaction"]
> [Tipos de interação e atalhos de teclado](drawing-tools-interactions-keyboard-shortcuts.md)

Saiba mais sobre as classes e os métodos usados neste artigo:

> [!div class="nextstepaction"]
> [Mapa](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.map?view=azure-iot-typescript-latest)

> [!div class="nextstepaction"]
> [Barra de ferramentas desenho](https://docs.microsoft.com/javascript/api/azure-maps-drawing-tools/atlas.control.drawingtoolbar?view=azure-node-latest)

> [!div class="nextstepaction"]
> [Gerenciador de desenho](https://docs.microsoft.com/javascript/api/azure-maps-drawing-tools/atlas.drawing.drawingmanager?view=azure-node-latest)