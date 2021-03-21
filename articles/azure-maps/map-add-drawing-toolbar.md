---
title: Adicione a barra de ferramentas de desenho para mapear | Microsoft Azure Maps
description: Como adicionar uma barra de ferramentas de desenho a um mapa usando Azure Maps Web SDK
author: anastasia-ms
ms.author: v-stharr
ms.date: 09/04/2019
ms.topic: conceptual
ms.service: azure-maps
services: azure-maps
manager: philmea
ms.custom: devx-track-js
ms.openlocfilehash: b00628ec5a9f41b027bf90b93421f3aa1404e97a
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/19/2021
ms.locfileid: "92896400"
---
# <a name="add-a-drawing-tools-toolbar-to-a-map"></a>Adicione uma barra de ferramentas de desenho a um mapa

Este artigo mostra-lhe como utilizar o módulo Ferramentas de Desenho e exibir a barra de ferramentas de desenho no mapa. O controlo [DrawingToolbar](/javascript/api/azure-maps-drawing-tools/atlas.control.drawingtoolbar) adiciona a barra de ferramentas de desenho no mapa. Você vai aprender a criar mapas com apenas uma e todas as ferramentas de desenho e como personalizar a renderização das formas de desenho no gestor de desenho.

## <a name="add-drawing-toolbar"></a>Adicionar barra de ferramentas de desenho

O código a seguir cria uma instância do gestor de desenho e exibe a barra de ferramentas no mapa.

```javascript
//Create an instance of the drawing manager and display the drawing toolbar.
drawingManager = new atlas.drawing.DrawingManager(map, {
        toolbar: new atlas.control.DrawingToolbar({
            position: 'top-right',
            style: 'dark'
        })
    });
```

Abaixo está a amostra completa do código de execução da funcionalidade acima:

<br/>

<iframe height="500" style="width: 100%;" scrolling="no" title="Adicionar barra de ferramentas de desenho" src="//codepen.io/azuremaps/embed/ZEzLeRg/?height=265&theme-id=0&default-tab=js,result&editable=true" frameborder='no' loading="lazy" allowtransparency="true" allowfullscreen="true">
Consulte a barra <a href='https://codepen.io/azuremaps/pen/ZEzLeRg/'>de ferramentas</a> de desenho Pen Add by Azure Maps <a href='https://codepen.io/azuremaps'>@azuremaps</a> () no <a href='https://codepen.io'>CodePen</a>.
</iframe>


## <a name="limit-displayed-toolbar-options"></a>Limite as opções de barra de ferramentas exibidas

O código a seguir cria uma instância do gestor de desenho e exibe a barra de ferramentas com apenas uma ferramenta de desenho de polígono no mapa. 

```javascript
//Create an instance of the drawing manager and display the drawing toolbar with polygon drawing tool.
drawingManager = new atlas.drawing.DrawingManager(map, {
        toolbar: new atlas.control.DrawingToolbar({
            position: 'top-right',
            style: 'light',
            buttons: ["draw-polygon"]
        })
    });
```

Abaixo está a amostra completa do código de execução da funcionalidade acima:

<br/>

<iframe height="500" style="width: 100%;" scrolling="no" title="Adicione uma ferramenta de desenho de polígono" src="//codepen.io/azuremaps/embed/OJLWWMy/?height=265&theme-id=0&default-tab=js,result&editable=true" frameborder='no' loading="lazy" allowtransparency="true" allowfullscreen="true">
Consulte a Pen <a href='https://codepen.io/azuremaps/pen/OJLWWMy/'>Adicione uma ferramenta de desenho de polígono</a> por Azure Maps <a href='https://codepen.io/azuremaps'>@azuremaps</a> () no <a href='https://codepen.io'>CodePen</a>.
</iframe>


## <a name="change-drawing-rendering-style"></a>Alterar estilo de renderização de desenho

O estilo das formas que são desenhadas pode ser personalizado recuperando as camadas subjacentes do gestor de desenho utilizando a `drawingManager.getLayers()` função e, em seguida, definindo opções nas camadas individuais. As pegas de arrasto que aparecem para as coordenadas ao editar uma forma são marcadores HTML. O estilo das pegas de arrasto pode ser personalizado passando as opções do marcador HTML para as `dragHandleStyle` `secondaryDragHandleStyle` opções e opções do gestor de desenho.  

O código seguinte obtém as camadas de renderização do gestor de desenho e modifica as suas opções para alterar o estilo de renderização para o desenho. Neste caso, os pontos serão renderizados com um ícone de marcador azul. As linhas serão vermelhas e quatro pixels de largura. Os polígonos terão uma cor de preenchimento verde e um contorno laranja. Em seguida, muda os estilos das pegas de arrasto para serem ícones quadrados. 

```javascript
//Get rendering layers of drawing manager.
var layers = drawingManager.getLayers();

//Change the icon rendered for points.
layers.pointLayer.setOptions({
    iconOptions: {
        image: 'marker-blue'
    }
});

//Change the color and width of lines.
layers.lineLayer.setOptions({
    strokeColor: 'red',
    strokeWidth: 4
});

//Change fill color of polygons.
layers.polygonLayer.setOptions({
    fillColor: 'green'
});

//Change the color of polygon outlines.
layers.polygonOutlineLayer.setOptions({
    strokeColor: 'orange'
});

//Update the style of the drag handles that appear when editting.
drawingManager.setOptions({
    //Primary drag handle that represents coordinates in the shape.
    dragHandleStyle: {
        anchor: 'center',
        htmlContent: '<svg width="15" height="15" viewBox="0 0 15 15" xmlns="http://www.w3.org/2000/svg" style="cursor:pointer"><rect x="0" y="0" width="15" height="15" style="stroke:black;fill:white;stroke-width:4px;"/></svg>',
        draggable: true
    },

    //Secondary drag hanle that represents mid-point coordinates that users can grab to add new cooridnates in the middle of segments.
    secondaryDragHandleStyle: {
        anchor: 'center',
        htmlContent: '<svg width="10" height="10" viewBox="0 0 10 10" xmlns="http://www.w3.org/2000/svg" style="cursor:pointer"><rect x="0" y="0" width="10" height="10" style="stroke:white;fill:black;stroke-width:4px;"/></svg>',
        draggable: true
    }
});  
```

Abaixo está a amostra completa do código de execução da funcionalidade acima:

<br/>

<iframe height="500" style="width: 100%;" scrolling="no" title="Alterar estilo de renderização de desenho" src="//codepen.io/azuremaps/embed/OJLWpyj/?height=265&theme-id=0&default-tab=js,result&editable=true" frameborder='no' loading="lazy" allowtransparency="true" allowfullscreen="true">
Consulte o <a href='https://codepen.io/azuremaps/pen/OJLWpyj/'>estilo de renderização de desenho</a> de Pen Change por Azure Maps <a href='https://codepen.io/azuremaps'>@azuremaps</a> () no <a href='https://codepen.io'>CodePen</a>.
</iframe>


## <a name="next-steps"></a>Passos seguintes

Saiba como utilizar características adicionais do módulo de ferramentas de desenho:

> [!div class="nextstepaction"]
> [Obter dados da forma](map-get-shape-data.md)

> [!div class="nextstepaction"]
> [Reagir a eventos de desenho](drawing-tools-events.md)

> [!div class="nextstepaction"]
> [Tipos de interação e atalhos de teclado](drawing-tools-interactions-keyboard-shortcuts.md)

Saiba mais sobre as aulas e métodos utilizados neste artigo:

> [!div class="nextstepaction"]
> [Mapa](/javascript/api/azure-maps-control/atlas.map)

> [!div class="nextstepaction"]
> [Barra de ferramentas de desenho](/javascript/api/azure-maps-drawing-tools/atlas.control.drawingtoolbar)

> [!div class="nextstepaction"]
> [Gestor de desenho](/javascript/api/azure-maps-drawing-tools/atlas.drawing.drawingmanager)