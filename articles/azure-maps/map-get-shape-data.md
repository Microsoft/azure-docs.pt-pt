---
title: Obtenha dados de formas num mapa | Microsoft Azure Maps
description: Neste artigo aprenda, como obter dados de forma desenhados num mapa usando o Microsoft Azure Maps Web SDK.
author: anastasia-ms
ms.author: v-stharr
ms.date: 09/04/2019
ms.topic: conceptual
ms.service: azure-maps
services: azure-maps
manager: philmea
ms.custom: devx-track-js
ms.openlocfilehash: ddb8009e544ede82d1c56d112950ff247a87380c
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/19/2021
ms.locfileid: "92890705"
---
# <a name="get-shape-data"></a>Obter dados da forma

Este artigo mostra-lhe como obter dados de formas que são desenhadas no mapa. Utilizamos a função **drawingManager.getSource ()** dentro [do gestor de desenho.](/javascript/api/azure-maps-drawing-tools/atlas.drawing.drawingmanager#getsource--) Existem vários cenários quando se pretende extrair dados geojson de uma forma desenhada e usá-lo em outro lugar.  


## <a name="get-data-from-drawn-shape"></a>Obtenha dados de forma desenhada

A seguinte função obtém os dados de origem da forma desenhada e os desemontes para o ecrã. 

```javascript
function getDrawnShapes() {
    var source = drawingManager.getSource();

    document.getElementById('CodeOutput').value = JSON.stringify(source.toJson(), null, '    ');
}
```

Abaixo está a amostra completa do código de execução, onde pode desenhar uma forma para testar a funcionalidade:

<br/>

<iframe height="686" title="Obter dados da forma" src="//codepen.io/azuremaps/embed/xxKgBVz/?height=265&theme-id=0&default-tab=result" frameborder='no' loading="lazy" allowtransparency="true" allowfullscreen="true" style='width: 100%;'>Consulte os <a href='https://codepen.io/azuremaps/pen/xxKgBVz/'>dados</a> de forma pen obter por Azure Maps ( <a href='https://codepen.io/azuremaps'>@azuremaps</a> ) no <a href='https://codepen.io'>CodePen</a>.
</iframe>


## <a name="next-steps"></a>Passos seguintes

Saiba como utilizar características adicionais do módulo de ferramentas de desenho:

> [!div class="nextstepaction"]
> [Reagir a eventos de desenho](drawing-tools-events.md)

> [!div class="nextstepaction"]
> [Tipos de interação e atalhos de teclado](drawing-tools-interactions-keyboard-shortcuts.md)

Saiba mais sobre as aulas e métodos utilizados neste artigo:

> [!div class="nextstepaction"]
> [Mapa](/javascript/api/azure-maps-control/atlas.map)

> [!div class="nextstepaction"]
> [Gestor de desenho](/javascript/api/azure-maps-drawing-tools/atlas.drawing.drawingmanager)

> [!div class="nextstepaction"]
> [Barra de ferramentas de desenho](/javascript/api/azure-maps-drawing-tools/atlas.control.drawingtoolbar)