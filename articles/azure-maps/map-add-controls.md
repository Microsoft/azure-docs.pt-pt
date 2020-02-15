---
title: Adicione controlos a um mapa  Microsoft Azure Maps
description: Como adicionar controlo de zoom, controlo de pitch, controlo rotativo e um picker de estilo para um mapa no Microsoft Azure Maps.
author: farah-alyasari
ms.author: v-faalya
ms.date: 07/29/2019
ms.topic: conceptual
ms.service: azure-maps
services: azure-maps
manager: timlt
ms.openlocfilehash: e386c136e7d0258d00c22c627dfa5047ba803169
ms.sourcegitcommit: 2823677304c10763c21bcb047df90f86339e476a
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 02/14/2020
ms.locfileid: "77209771"
---
# <a name="add-controls-to-a-map"></a>Adicione controlos a um mapa

Este artigo mostra-lhe como adicionar controlos a um mapa. Você também vai aprender a criar um mapa com todos os controlos e um apanhador de [estilo](https://docs.microsoft.com/azure/azure-maps/choose-map-style).

## <a name="add-zoom-control"></a>Adicionar controlo de zoom

Um controlo de zoom adiciona botões para ampliar o mapa para dentro e para fora. A amostra de código seguinte cria uma instância da classe [ZoomControl,](/javascript/api/azure-maps-control/atlas.control.zoomcontrol) e adiciona-a no canto inferior direito do mapa.

```javascript
//Construct a zoom control and add it to the map.
map.controls.add(new atlas.control.ZoomControl(), {
    position: 'bottom-right'
});
```

Abaixo está a amostra completa de código de execução da funcionalidade acima referida.

<br/>

<iframe height='500' scrolling='no' title='Adicionar um controlo de zoom' src='//codepen.io/azuremaps/embed/WKOQyN/?height=265&theme-id=0&default-tab=js,result&embed-version=2&editable=true' frameborder='no' allowtransparency='true' allowfullscreen='true' style='width: 100%;'>Consulte a caneta <a href='https://codepen.io/azuremaps/pen/WKOQyN/'>Adicionando um controlo de zoom</a> por Azure Maps<a href='https://codepen.io/azuremaps'> (@azuremaps</a>) no <a href='https://codepen.io'>CodePen</a>.
</iframe>

## <a name="add-pitch-control"></a>Adicionar controlo de pitch

Um controlo de arremesso adiciona botões para inclinar o tom para mapear em relação ao horizonte. A amostra de código seguinte cria uma instância da classe [PitchControl.](/javascript/api/azure-maps-control/atlas.control.pitchcontrol) Adiciona o PitchControl ao canto superior direito do mapa.

```javascript
//Construct a pitch control and add it to the map.
map.controls.add(new atlas.control.PitchControl(), {
    position: 'top-right'
});
```

Abaixo está a amostra completa de código de execução da funcionalidade acima referida.

<br/>

<iframe height='500' scrolling='no' title='Adicionar um controlo de pitch' src='//codepen.io/azuremaps/embed/xJrwaP/?height=265&theme-id=0&default-tab=js,result&embed-version=2&editable=true' frameborder='no' allowtransparency='true' allowfullscreen='true' style='width: 100%;'>Consulte a Caneta <a href='https://codepen.io/azuremaps/pen/xJrwaP/'>Adicionando um controlo</a> de pitch por Azure Maps<a href='https://codepen.io/azuremaps'> (@azuremaps</a>) no <a href='https://codepen.io'>CodePen</a>.
</iframe>

## <a name="add-compass-control"></a>Adicionar controlo de bússola

Um controlo de bússola adiciona um botão para rodar o mapa. A amostra de código seguinte cria uma instância da classe [Decontrolo](/javascript/api/azure-maps-control/atlas.control.compasscontrol) da Bússola e adiciona-a ao canto inferior esquerdo do mapa.

```javascript
//Construct a compass control and add it to the map.
map.controls.add(new atlas.control.Compass(), {
    position: 'bottom-left'
});
```

Abaixo está a amostra completa de código de execução da funcionalidade acima referida.

<br/>

<iframe height='500' scrolling='no' title='Adicionar um controlo rotativo' src='//codepen.io/azuremaps/embed/GBEoRb/?height=265&theme-id=0&default-tab=js,result&embed-version=2&editable=true' frameborder='no' allowtransparency='true' allowfullscreen='true' style='width: 100%;'>Consulte a caneta <a href='https://codepen.io/azuremaps/pen/GBEoRb/'>Adicionando um controlo rotativo</a> por Azure Maps<a href='https://codepen.io/azuremaps'> (@azuremaps</a>) no <a href='https://codepen.io'>CodePen</a>.
</iframe>

## <a name="a-map-with-all-controls"></a>Um mapa com todos os controlos

A amostra de código seguinte adiciona os controlos de picker de estilo, zoom, pitch e bússola ao canto inferior direito do mapa. Reparem como se empilham automaticamente. A ordem dos objetos de controlo no guião dita a ordem em que aparecem no mapa. Para alterar a ordem dos controlos no mapa, pode alterar a ordem no script.

<br/>

<iframe height='500' scrolling='no' title='Um mapa com todos os controlos' src='//codepen.io/azuremaps/embed/qyjbOM/?height=265&theme-id=0&default-tab=js,result&embed-version=2&editable=true' frameborder='no' allowtransparency='true' allowfullscreen='true' style='width: 100%;'>Veja o mapa pen <a href='https://codepen.io/azuremaps/pen/qyjbOM/'>A com todos os controlos do</a> Azure Maps<a href='https://codepen.io/azuremaps'> (@azuremaps</a>) no <a href='https://codepen.io'>CodePen</a>.
</iframe>

O controlo do picker de estilo é definido pela classe [StyleControl.](/javascript/api/azure-maps-control/atlas.control.stylecontrol) Para obter mais informações sobre a utilização do controlo do picker de estilo, consulte [escolha um estilo](choose-map-style.md)de mapa .

## <a name="customize-controls"></a>Personalizar controlos

Aqui está uma ferramenta para testar as várias opções para personalizar os controlos.

<br/>

<iframe height="700" style="width: 100%;" scrolling="no" title="Opções de controlo de navegação" src="//codepen.io/azuremaps/embed/LwBZMx/?height=700&theme-id=0&default-tab=result" frameborder="no" allowtransparency="true" allowfullscreen="true">
Consulte as <a href='https://codepen.io/azuremaps/pen/LwBZMx/'>opções</a> de controlo de navegação por Caneta saque a Azure Maps<a href='https://codepen.io/azuremaps'> (@azuremaps</a>) no <a href='https://codepen.io'>CodePen</a>.
</iframe>

Se quiser criar controlos de navegação personalizados, crie uma classe que se estenda à classe `atlas.Control` ou crie um elemento HTML e o posicione acima do div do mapa. Mande este controlo de UI ligar para os mapas `setCamera` função para mover o mapa. 

## <a name="next-steps"></a>Passos seguintes

Saiba mais sobre as aulas e métodos utilizados neste artigo:

> [!div class="nextstepaction"]
> [Controlo da Bússola](/javascript/api/azure-maps-control/atlas.control.compasscontrol)

> [!div class="nextstepaction"]
> [PitchControl](/javascript/api/azure-maps-control/atlas.control.pitchcontrol) 

> [!div class="nextstepaction"]
> [StyleControl](/javascript/api/azure-maps-control/atlas.control.stylecontrol) 

> [!div class="nextstepaction"]
> [ZoomControl](/javascript/api/azure-maps-control/atlas.control.zoomcontrol) 

Consulte os seguintes artigos para obter o código completo:

> [!div class="nextstepaction"]
> [Adicione um pino](./map-add-pin.md)

> [!div class="nextstepaction"]
> [Adicione um popup](./map-add-popup.md)

> [!div class="nextstepaction"]
> [Adicione uma camada de linha](map-add-line-layer.md)

> [!div class="nextstepaction"]
> [Adicione uma camada de polígono](map-add-shape.md)

> [!div class="nextstepaction"]
> [Adicione uma camada de bolha](map-add-bubble-layer.md)

