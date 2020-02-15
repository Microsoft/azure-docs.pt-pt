---
title: Obtenha dados de formas num mapa Microsoft Azure Maps
description: Neste artigo aprende, como obter dados de forma desenhados num mapa usando o Microsoft Azure Maps Web SDK.
author: farah-alyasari
ms.author: v-faalya
ms.date: 09/04/2019
ms.topic: conceptual
ms.service: azure-maps
services: azure-maps
manager: philmea
ms.openlocfilehash: 63d947b85e75e3809445c5bc65577aeaed38caa1
ms.sourcegitcommit: 2823677304c10763c21bcb047df90f86339e476a
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 02/14/2020
ms.locfileid: "77209669"
---
# <a name="get-shape-data"></a>Obter dados da forma

Este artigo mostra-lhe como obter dados de formas que são desenhadas no mapa. Utilizamos a função **drawingManager.getSource()** dentro [do gestor de desenho](https://docs.microsoft.com/javascript/api/azure-maps-drawing-tools/atlas.drawing.drawingmanager?view=azure-node-latest#getsource--). Existem vários cenários quando se pretende extrair dados geojson de uma forma desenhada e usá-lo em outros lugares.  


## <a name="get-data-from-drawn-shape"></a>Obtenha dados da forma desenhada

A função seguinte obtém os dados de origem da forma desenhada e os produz para o ecrã. 

```Javascript
function getDrawnShapes() {
    var source = drawingManager.getSource();

    document.getElementById('CodeOutput').value = JSON.stringify(source.toJson(), null, '    ');
}
```

Abaixo está a amostra completa do código de execução, onde pode desenhar uma forma para testar a funcionalidade:

<br/>

<iframe height="686" title="Obter dados da forma" src="//codepen.io/azuremaps/embed/xxKgBVz/?height=265&theme-id=0&default-tab=result" frameborder="no" allowtransparency="true" allowfullscreen="true" style='width: 100%;'>Consulte os dados de forma da Pen <a href='https://codepen.io/azuremaps/pen/xxKgBVz/'>Get</a> por Azure Maps<a href='https://codepen.io/azuremaps'> (@azuremaps</a>) no <a href='https://codepen.io'>CodePen</a>.
</iframe>


## <a name="next-steps"></a>Passos seguintes

Aprenda a utilizar funcionalidades adicionais do módulo de ferramentas de desenho:

> [!div class="nextstepaction"]
> [Reagir a eventos de desenho](drawing-tools-events.md)

> [!div class="nextstepaction"]
> [Tipos de interação e atalhos de teclado](drawing-tools-interactions-keyboard-shortcuts.md)

Saiba mais sobre as aulas e métodos utilizados neste artigo:

> [!div class="nextstepaction"]
> [Mapa](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.map?view=azure-iot-typescript-latest)

> [!div class="nextstepaction"]
> [Gestor de desenho](https://docs.microsoft.com/javascript/api/azure-maps-drawing-tools/atlas.drawing.drawingmanager?view=azure-node-latest)

> [!div class="nextstepaction"]
> [Barra de ferramentas de desenho](https://docs.microsoft.com/javascript/api/azure-maps-drawing-tools/atlas.control.drawingtoolbar?view=azure-node-latest)
