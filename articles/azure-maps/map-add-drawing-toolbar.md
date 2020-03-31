---
title: Adicione uma barra de ferramentas de desenho a um mapa Microsoft Azure Maps
description: Como adicionar uma barra de ferramentas de desenho a um mapa usando O Web SDK do Azure Maps
author: philmea
ms.author: philmea
ms.date: 09/04/2019
ms.topic: conceptual
ms.service: azure-maps
services: azure-maps
manager: philmea
ms.openlocfilehash: bebf1ddfbca3aec5a551193609381cf3510bc3ac
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/28/2020
ms.locfileid: "80334489"
---
# <a name="add-a-drawing-tools-toolbar-to-a-map"></a>Adicione uma barra de ferramentas de desenho a um mapa

Este artigo mostra-lhe como usar o módulo Ferramentas de Desenho e exibir a barra de ferramentas de desenho no mapa. O comando [da barra de desenho](https://docs.microsoft.com/javascript/api/azure-maps-drawing-tools/atlas.control.drawingtoolbar?view=azure-node-latest) adiciona a barra de ferramentas de desenho no mapa. Você vai aprender a criar mapas com apenas uma e todas as ferramentas de desenho e como personalizar a renderização das formas de desenho no gestor de desenho.

## <a name="add-drawing-toolbar"></a>Adicionar barra de ferramentas de desenho

O código seguinte cria uma instância do gestor de desenho e exibe a barra de ferramentas no mapa.

```javascript
//Create an instance of the drawing manager and display the drawing toolbar.
drawingManager = new atlas.drawing.DrawingManager(map, {
        toolbar: new atlas.control.DrawingToolbar({
            position: 'top-right',
            style: 'dark'
        })
    });
```

Abaixo está a amostra completa de código de execução da funcionalidade acima:

<br/>

<iframe height="500" style="width: 100%;" scrolling="no" title="Adicionar barra de ferramentas de desenho" src="//codepen.io/azuremaps/embed/ZEzLeRg/?height=265&theme-id=0&default-tab=js,result&editable=true" frameborder="no" allowtransparency="true" allowfullscreen="true">
Consulte a barra de <a href='https://codepen.io/azuremaps/pen/ZEzLeRg/'>ferramentas</a> de<a href='https://codepen.io/azuremaps'>@azuremaps</a>desenho Pen Add by Azure Maps () no <a href='https://codepen.io'>CodePen</a>.
</iframe>


## <a name="limit-displayed-toolbar-options"></a>Limitar as opções de barra de ferramentas apresentadas

O código seguinte cria uma instância do gestor de desenho e exibe a barra de ferramentas com apenas uma ferramenta de desenho de polígono no mapa. 

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

Abaixo está a amostra completa de código de execução da funcionalidade acima:

<br/>

<iframe height="500" style="width: 100%;" scrolling="no" title="Adicione uma ferramenta de desenho de polígono" src="//codepen.io/azuremaps/embed/OJLWWMy/?height=265&theme-id=0&default-tab=js,result&editable=true" frameborder="no" allowtransparency="true" allowfullscreen="true">
Consulte a caneta Adicione uma ferramenta de desenho<a href='https://codepen.io/azuremaps'>@azuremaps</a>de <a href='https://codepen.io/azuremaps/pen/OJLWWMy/'>polígono</a> por Azure Maps () em <a href='https://codepen.io'>CodePen</a>.
</iframe>


## <a name="change-drawing-rendering-style"></a>Alterar o estilo de renderização de desenho

O estilo das formas que são desenhadas pode ser personalizado recuperando as `drawingManager.getLayers()` camadas subjacentes do gestor de desenho utilizando a função e, em seguida, definindo opções nas camadas individuais. As pegas de arrasto que aparecem para as coordenadas ao editar uma forma são marcadores HTML. O estilo das pegas de arrasto pode ser personalizado `dragHandleStyle` `secondaryDragHandleStyle` passando as opções de marcador HTML para as opções e opções do gestor de desenho.  

O código seguinte obtém as camadas de renderização do gestor de desenho e modifica as suas opções para alterar o estilo de renderização para desenhar. Neste caso, os pontos serão renderizados com um ícone de marcador azul. As linhas serão vermelhas e quatro pixels de largura. Os polígonos terão uma cor de preenchimento verde e um contorno laranja. Em seguida, muda os estilos das pegas de arrasto para serem ícones quadrados. 

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

Abaixo está a amostra completa de código de execução da funcionalidade acima:

<br/>

<iframe height="500" style="width: 100%;" scrolling="no" title="Alterar o estilo de renderização de desenho" src="//codepen.io/azuremaps/embed/OJLWpyj/?height=265&theme-id=0&default-tab=js,result&editable=true" frameborder="no" allowtransparency="true" allowfullscreen="true">
Veja o estilo de <a href='https://codepen.io/azuremaps/pen/OJLWpyj/'>renderização</a> de<a href='https://codepen.io/azuremaps'>@azuremaps</a>desenho pen Change por Azure Maps () no <a href='https://codepen.io'>CodePen</a>.
</iframe>


## <a name="next-steps"></a>Passos seguintes

Aprenda a utilizar funcionalidades adicionais do módulo de ferramentas de desenho:

> [!div class="nextstepaction"]
> [Obter dados da forma](map-get-shape-data.md)

> [!div class="nextstepaction"]
> [Reagir a eventos de desenho](drawing-tools-events.md)

> [!div class="nextstepaction"]
> [Tipos de interação e atalhos de teclado](drawing-tools-interactions-keyboard-shortcuts.md)

Saiba mais sobre as aulas e métodos utilizados neste artigo:

> [!div class="nextstepaction"]
> [Mapa](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.map?view=azure-iot-typescript-latest)

> [!div class="nextstepaction"]
> [Barra de ferramentas de desenho](https://docs.microsoft.com/javascript/api/azure-maps-drawing-tools/atlas.control.drawingtoolbar?view=azure-node-latest)

> [!div class="nextstepaction"]
> [Gestor de desenho](https://docs.microsoft.com/javascript/api/azure-maps-drawing-tools/atlas.drawing.drawingmanager?view=azure-node-latest)
