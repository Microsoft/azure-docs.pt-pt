---
title: Obter dados de formas do Gerenciador de desenho no Azure Maps | Microsoft Docs
description: Como obter dados de forma usando o SDK da Web do Azure Maps
author: walsehgal
ms.author: v-musehg
ms.date: 09/04/2019
ms.topic: conceptual
ms.service: azure-maps
services: azure-maps
manager: philmea
ms.openlocfilehash: e7706fba1efad1bd0ce7110e129dcf113689af9a
ms.sourcegitcommit: f176e5bb926476ec8f9e2a2829bda48d510fbed7
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 09/04/2019
ms.locfileid: "70309761"
---
# <a name="get-shape-data"></a>Obter dados da forma

Este artigo mostra como obter dados de formas que foram desenhadas no mapa usando a função [](https://docs.microsoft.com/javascript/api/azure-maps-drawing-tools/atlas.drawing.drawingmanager?view=azure-node-latest#getsource--) **drawingmanager. GetSource ()** do Gerenciador de desenho. Pode haver vários cenários em que você talvez queira extrair dados geojson de uma forma desenhada e utilizá-los em outro lugar.  


## <a name="get-data-from-drawn-shape"></a>Obter dados da forma desenhada

A função a seguir obtém os dados de origem da forma desenhada e as gera na tela. 

```Javascript
function getDrawnShapes() {
    var source = drawingManager.getSource();

    document.getElementById('CodeOutput').value = JSON.stringify(source.toJson(), null, '    ');
}
```

Abaixo está o exemplo de código completo em execução, onde você pode desenhar uma forma para testar a funcionalidade:

<br/>

<iframe height="686" title="Obter dados da forma" src="//codepen.io/azuremaps/embed/xxKgBVz/?height=265&theme-id=0&default-tab=result" frameborder="no" allowtransparency="true" allowfullscreen="true" style='width: 100%;'>Consulte a caneta <a href='https://codepen.io/azuremaps/pen/xxKgBVz/'>obter dados de forma</a> por mapas do<a href='https://codepen.io/azuremaps'>@azuremaps</a>Azure () em <a href='https://codepen.io'>CodePen</a>.
</iframe>


## <a name="next-steps"></a>Passos Seguintes

Saiba mais sobre as classes e os métodos usados neste artigo:

> [!div class="nextstepaction"]
> [Mapeada](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.map?view=azure-iot-typescript-latest)

> [!div class="nextstepaction"]
> [Gerenciador de desenho](https://docs.microsoft.com/javascript/api/azure-maps-drawing-tools/atlas.drawing.drawingmanager?view=azure-node-latest)

> [!div class="nextstepaction"]
> [Barra de ferramentas desenho](https://docs.microsoft.com/javascript/api/azure-maps-drawing-tools/atlas.control.drawingtoolbar?view=azure-node-latest)
