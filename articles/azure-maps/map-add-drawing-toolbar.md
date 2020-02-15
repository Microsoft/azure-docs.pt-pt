---
title: Adicione uma barra de ferramentas de desenho a um mapa Microsoft Azure Maps
description: Como adicionar uma barra de ferramentas de desenho a um mapa usando O Web SDK do Azure Maps
author: farah-alyasari
ms.author: v-faalya
ms.date: 09/04/2019
ms.topic: conceptual
ms.service: azure-maps
services: azure-maps
manager: philmea
ms.openlocfilehash: cb0f70bc42c9ac0f7026c910593950516f027a88
ms.sourcegitcommit: 2823677304c10763c21bcb047df90f86339e476a
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 02/14/2020
ms.locfileid: "77209754"
---
# <a name="add-a-drawing-tools-toolbar-to-a-map"></a>Adicione uma barra de ferramentas de desenho a um mapa

Este artigo mostra-lhe como usar o módulo Ferramentas de Desenho e exibir a barra de ferramentas de desenho no mapa. O comando [da barra de desenho](https://docs.microsoft.com/javascript/api/azure-maps-drawing-tools/atlas.control.drawingtoolbar?view=azure-node-latest) adiciona a barra de ferramentas de desenho no mapa. Você vai aprender a criar mapas com apenas uma e todas as ferramentas de desenho e como personalizar a renderização das formas de desenho no gestor de desenho.

## <a name="add-drawing-toolbar"></a>Adicionar barra de ferramentas de desenho

O código seguinte cria uma instância do gestor de desenho e exibe a barra de ferramentas no mapa.

```Javascript
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
Consulte a barra de <a href='https://codepen.io/azuremaps/pen/ZEzLeRg/'>ferramentas de desenho</a> Pen Add by Azure Maps<a href='https://codepen.io/azuremaps'> (@azuremaps</a>) no <a href='https://codepen.io'>CodePen</a>.
</iframe>


## <a name="limit-displayed-toolbar-options"></a>Limitar as opções de barra de ferramentas apresentadas

O código seguinte cria uma instância do gestor de desenho e exibe a barra de ferramentas com apenas uma ferramenta de desenho de polígono no mapa. 

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

Abaixo está a amostra completa de código de execução da funcionalidade acima:

<br/>

<iframe height="500" style="width: 100%;" scrolling="no" title="Adicione uma ferramenta de desenho de polígono" src="//codepen.io/azuremaps/embed/OJLWWMy/?height=265&theme-id=0&default-tab=js,result&editable=true" frameborder="no" allowtransparency="true" allowfullscreen="true">
Consulte a caneta Adicione uma ferramenta de desenho de <a href='https://codepen.io/azuremaps/pen/OJLWWMy/'>polígono</a> da Azure Maps<a href='https://codepen.io/azuremaps'> (@azuremaps</a>) no <a href='https://codepen.io'>CodePen</a>.
</iframe>


## <a name="change-drawing-rendering-style"></a>Alterar o estilo de renderização de desenho

O código seguinte obtém as camadas de renderização do gestor de desenho e modifica as suas opções para alterar o estilo de renderização para desenhar. Neste caso, os pontos serão renderizados com um ícone de marcador azul. As linhas serão vermelhas e quatro pixels de largura. Os polígonos terão uma cor de preenchimento verde e um contorno laranja.

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

Abaixo está a amostra completa de código de execução da funcionalidade acima:

<br/>

<iframe height="500" style="width: 100%;" scrolling="no" title="Alterar o estilo de renderização de desenho" src="//codepen.io/azuremaps/embed/OJLWpyj/?height=265&theme-id=0&default-tab=js,result&editable=true" frameborder="no" allowtransparency="true" allowfullscreen="true">
Veja o estilo de <a href='https://codepen.io/azuremaps/pen/OJLWpyj/'>renderização</a> de desenho pen Change da Azure Maps<a href='https://codepen.io/azuremaps'> (@azuremaps</a>) no <a href='https://codepen.io'>CodePen</a>.
</iframe>


## <a name="next-steps"></a>Passos seguintes

Aprenda a utilizar funcionalidades adicionais do módulo de ferramentas de desenho:

> [!div class="nextstepaction"]
> [Obtenha dados de forma](map-get-shape-data.md)

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
