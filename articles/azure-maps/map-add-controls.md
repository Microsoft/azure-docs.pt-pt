---
title: Adicione controlos a um mapa Microsoft Azure Maps
description: Como adicionar controlo de zoom, controlo de tom, controlo de rotação e um picker de estilo para um mapa no Microsoft Azure Maps.
author: anastasia-ms
ms.author: v-stharr
ms.date: 07/29/2019
ms.topic: conceptual
ms.service: azure-maps
services: azure-maps
manager: timlt
ms.custom: devx-track-js
ms.openlocfilehash: 08a3b72bb137713721345ecb52cf4e1bb7afcdc2
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/09/2020
ms.locfileid: "91310769"
---
# <a name="add-controls-to-a-map"></a>Adicionar controlos a um mapa

Este artigo mostra-lhe como adicionar controlos a um mapa. Você também vai aprender a criar um mapa com todos os controlos e um [picker de estilo](https://docs.microsoft.com/azure/azure-maps/choose-map-style).

## <a name="add-zoom-control"></a>Adicionar controlo de zoom

Um controlo de zoom adiciona botões para fazer zoom no mapa para dentro e para fora. A amostra de código a seguir cria uma instância da classe [ZoomControl](/javascript/api/azure-maps-control/atlas.control.zoomcontrol) e adiciona-a ao canto inferior direito do mapa.

```javascript
//Construct a zoom control and add it to the map.
map.controls.add(new atlas.control.ZoomControl(), {
    position: 'bottom-right'
});
```

Abaixo está a amostra completa do código de execução da funcionalidade acima.

<br/>

<iframe height='500' scrolling='no' title='Adicionar um controlo de zoom' src='//codepen.io/azuremaps/embed/WKOQyN/?height=265&theme-id=0&default-tab=js,result&embed-version=2&editable=true' frameborder='no' loading="lazy" allowtransparency='true' allowfullscreen='true' style='width: 100%;'>Ver a Caneta <a href='https://codepen.io/azuremaps/pen/WKOQyN/'>Adicionando um controlo de zoom</a> por Azure Maps <a href='https://codepen.io/azuremaps'>@azuremaps</a> () no <a href='https://codepen.io'>CodePen</a>.
</iframe>

## <a name="add-pitch-control"></a>Adicione o controlo do tom

Um controlo de arremesso adiciona botões para inclinar o tom para mapear em relação ao horizonte. A seguinte amostra de código cria uma instância da classe [PitchControl.](/javascript/api/azure-maps-control/atlas.control.pitchcontrol) Adiciona o PitchControl ao canto superior direito do mapa.

```javascript
//Construct a pitch control and add it to the map.
map.controls.add(new atlas.control.PitchControl(), {
    position: 'top-right'
});
```

Abaixo está a amostra completa do código de execução da funcionalidade acima.

<br/>

<iframe height='500' scrolling='no' title='Adicionar um controlo de arremesso' src='//codepen.io/azuremaps/embed/xJrwaP/?height=265&theme-id=0&default-tab=js,result&embed-version=2&editable=true' frameborder='no' loading="lazy" allowtransparency='true' allowfullscreen='true' style='width: 100%;'>Ver a Caneta <a href='https://codepen.io/azuremaps/pen/xJrwaP/'>Adicionando um controlo de tom</a> por Azure Maps <a href='https://codepen.io/azuremaps'>@azuremaps</a> () no <a href='https://codepen.io'>CodePen</a>.
</iframe>

## <a name="add-compass-control"></a>Adicionar controlo de bússola

Um controlo da bússola adiciona um botão para rodar o mapa. A amostra de código a seguir cria uma instância da classe [de Controlo de Bússola](/javascript/api/azure-maps-control/atlas.control.compasscontrol) e adiciona-a ao canto inferior esquerdo do mapa.

```javascript
//Construct a compass control and add it to the map.
map.controls.add(new atlas.control.Compass(), {
    position: 'bottom-left'
});
```

Abaixo está a amostra completa do código de execução da funcionalidade acima.

<br/>

<iframe height='500' scrolling='no' title='Adicionar um controlo rotativo' src='//codepen.io/azuremaps/embed/GBEoRb/?height=265&theme-id=0&default-tab=js,result&embed-version=2&editable=true' frameborder='no' loading="lazy" allowtransparency='true' allowfullscreen='true' style='width: 100%;'>Ver a caneta <a href='https://codepen.io/azuremaps/pen/GBEoRb/'>Adicionar um controlo rotativo</a> por Azure Maps <a href='https://codepen.io/azuremaps'>@azuremaps</a> () no <a href='https://codepen.io'>CodePen</a>.
</iframe>

## <a name="a-map-with-all-controls"></a>Um mapa com todos os controlos

Vários controlos podem ser colocados numa matriz e adicionados ao mapa de uma só vez e posicionados na mesma área do mapa para simplificar o desenvolvimento. O seguinte adiciona os controlos de navegação padrão ao mapa usando esta abordagem.

```javascript
map.controls.add([
    new atlas.control.ZoomControl(),
    new atlas.control.CompassControl(),
    new atlas.control.PitchControl(),
    new atlas.control.StyleControl()
], {
    position: "top-right"
});
```

A amostra de código a seguir adiciona os controlos de zoom, bússola, pitch e estilo ao canto superior direito do mapa. Reparem como se empilham automaticamente. A ordem dos objetos de controlo no script dita a ordem em que aparecem no mapa. Para alterar a ordem dos controlos no mapa, pode alterar a sua ordem na matriz.

<br/>

<iframe height='500' scrolling='no' title='Um mapa com todos os controlos' src='//codepen.io/azuremaps/embed/qyjbOM/?height=265&theme-id=0&default-tab=js,result&embed-version=2&editable=true' frameborder='no' loading="lazy" allowtransparency='true' allowfullscreen='true' style='width: 100%;'>Consulte o mapa Pen <a href='https://codepen.io/azuremaps/pen/qyjbOM/'>A com todos os controlos</a> de Azure Maps <a href='https://codepen.io/azuremaps'>@azuremaps</a> () no <a href='https://codepen.io'>CodePen</a>.
</iframe>

O controlo de picker de estilo é definido pela classe [StyleControl.](/javascript/api/azure-maps-control/atlas.control.stylecontrol) Para obter mais informações sobre a utilização do controlo de picker de estilo, consulte [escolher um estilo de mapa](choose-map-style.md).

## <a name="customize-controls"></a>Personalizar controlos

Aqui está uma ferramenta para testar as várias opções para personalizar os controlos.

<br/>

<iframe height="700" style="width: 100%;" scrolling="no" title="Opções de controlo de navegação" src="//codepen.io/azuremaps/embed/LwBZMx/?height=700&theme-id=0&default-tab=result" frameborder='no' loading="lazy" allowtransparency="true" allowfullscreen="true">
Consulte as opções de <a href='https://codepen.io/azuremaps/pen/LwBZMx/'>controlo de navegação de</a> canetas por Azure Maps ( ) no <a href='https://codepen.io/azuremaps'>@azuremaps</a> <a href='https://codepen.io'>CodePen</a>.
</iframe>

Se pretender criar controlos de navegação personalizados, crie uma classe que se estenda desde a `atlas.Control` classe ou crie um elemento HTML e posicione-o acima da div do mapa. Que este controlo de UI chame a função de mapas `setCamera` para mover o mapa. 

## <a name="next-steps"></a>Passos seguintes

Saiba mais sobre as aulas e métodos utilizados neste artigo:

> [!div class="nextstepaction"]
> [Controlo de bússolas](/javascript/api/azure-maps-control/atlas.control.compasscontrol)

> [!div class="nextstepaction"]
> [PitchControl](/javascript/api/azure-maps-control/atlas.control.pitchcontrol) 

> [!div class="nextstepaction"]
> [StyleControl](/javascript/api/azure-maps-control/atlas.control.stylecontrol) 

> [!div class="nextstepaction"]
> [ZoomControl](/javascript/api/azure-maps-control/atlas.control.zoomcontrol) 

Consulte os seguintes artigos para obter o código completo:

> [!div class="nextstepaction"]
> [Adicionar um pin](./map-add-pin.md)

> [!div class="nextstepaction"]
> [Adicionar um pop-up](./map-add-popup.md)

> [!div class="nextstepaction"]
> [Adicionar uma camada de linhas](map-add-line-layer.md)

> [!div class="nextstepaction"]
> [Adicionar uma camada de polígonos](map-add-shape.md)

> [!div class="nextstepaction"]
> [Adicionar uma camada de bolha](map-add-bubble-layer.md)

