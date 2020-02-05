---
title: Manipular eventos de mapa | Mapas do Microsoft Azure
description: Neste artigo, você aprenderá a fazer um mapa Web SDK interativo com eventos de mapas usando o Web SDK do Microsoft Azure Maps.
author: jingjing-z
ms.author: jinzh
ms.date: 09/10/2019
ms.topic: conceptual
ms.service: azure-maps
services: azure-maps
manager: ''
ms.custom: codepen
ms.openlocfilehash: 06fdb9733ad9c173c88936e5502214b58f5d10ee
ms.sourcegitcommit: 4f6a7a2572723b0405a21fea0894d34f9d5b8e12
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 02/04/2020
ms.locfileid: "76985922"
---
# <a name="interact-with-the-map"></a>Interagir com o mapa

Este artigo mostra-lhe como usar a classe de [eventos de mapas.](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.map?#events) A propriedade destaca eventos no mapa e em diferentes camadas do mapa. Também pode destacar eventos quando interage com um marcador HTML.

## <a name="interact-with-the-map"></a>Interagir com o mapa

Jogue com o mapa abaixo e veja os eventos de mouse correspondentes realçados à direita. Você pode clicar na **guia js** para exibir e editar o código JavaScript. Também pode clicar em **Editar no CodePen** para modificar o código no CodePen.

<br/>

<iframe height='600' scrolling='no' title='Interagindo com o mapa – eventos de mouse' src='//codepen.io/azuremaps/embed/bLZEWd/?height=600&theme-id=0&default-tab=js,result&embed-version=2&editable=true' frameborder='no' allowtransparency='true' allowfullscreen='true' style='width: 100%;'>Consulte a caneta <a href='https://codepen.io/azuremaps/pen/bLZEWd/'>interagir com o mapa – eventos de mouse</a> pelo Azure Maps (<a href='https://codepen.io/azuremaps'>@azuremaps</a>) em <a href='https://codepen.io'>CodePen</a>.
</iframe>

## <a name="interact-with-map-layers"></a>Interagir com as camadas do mapa

O código seguinte destaca o evento disparado enquanto interage com a Camada de Símbolo. O símbolo, a bolha, a linha e a camada de polígono oferecem suporte ao mesmo conjunto de eventos. O mapa de calor e as camadas de azulejos não suportam nenhum destes eventos.

<br/>

<iframe height='600' scrolling='no' title='Interagindo com o mapa – eventos de camada' src='//codepen.io/azuremaps/embed/bQRRPE/?height=600&theme-id=0&default-tab=js,result&embed-version=2&editable=true' frameborder='no' allowtransparency='true' allowfullscreen='true' style='width: 100%;'>Consulte a caneta <a href='https://codepen.io/azuremaps/pen/bQRRPE/'>interagindo com os eventos de camada do mapa</a> pelo Azure Maps (<a href='https://codepen.io/azuremaps'>@azuremaps</a>) em <a href='https://codepen.io'>CodePen</a>.
</iframe>

## <a name="interact-with-html-marker"></a>Interagir com marcador HTML

O código a seguir adiciona eventos de mapa JavaScript a um marcador HTML. Ele também realça o nome dos eventos que são acionados enquanto você interage com o marcador HTML.

<br/>

<iframe height='500' scrolling='no' title='Interagindo com o mapa-eventos de marcador HTML' src='//codepen.io/azuremaps/embed/VVzKJY/?height=500&theme-id=0&default-tab=js,result&embed-version=2&editable=true' frameborder='no' allowtransparency='true' allowfullscreen='true' style='width: 100%;'>Veja a caneta <a href='https://codepen.io/azuremaps/pen/VVzKJY/'>interagindo com os eventos do marcador MAP-HTML</a> pelo Azure Maps (<a href='https://codepen.io/azuremaps'>@azuremaps</a>) em <a href='https://codepen.io'>CodePen</a>.
</iframe>

A tabela a seguir lista todos os eventos de classe de mapa com suporte.

| Evento               | Descrição |
|---------------------|-------------|
| `boxzoomend`        | Acionado quando uma interação de "caixa de zoom" termina.|
| `boxzoomstart`      | Acionado quando uma interação "caixa de zoom" é iniciada.|
| `click`             | Acionado quando um dispositivo apontador é pressionado e liberado no mesmo ponto no mapa.|
| `close`             | Acionado quando o popup é fechado manualmente ou programaticamente.|
| `contextmenu`       | Acionado quando o botão direito do mouse é clicado.|
| `data`              | Acionado quando os dados do mapa são carregados ou alterados. |
| `dataadded`         | Acionado quando formas são adicionadas ao `DataSource`.|
| `dataremoved`       | Acionado quando as formas são removidas da `DataSource`.|
| `datasourceupdated` | Acionado quando o objeto de `DataSource` é atualizado.|
| `dblclick`          | Acionado quando um dispositivo apontador é clicado duas vezes no mesmo ponto no mapa.|
| `drag`              | Acionado repetidamente durante uma interação "arrastar para panorâmica" no marcador de mapa, pop-up ou HTML.|
| `dragend`           | Acionado quando uma interação "arrastar para a panorâmica" termina no marcador de mapa, pop-up ou HTML.|
| `dragstart`         | Acionado quando uma interação "arrastar para a panorâmica" começa no marcador de mapa, pop-up ou HTML.|
| `error`             | Acionado quando ocorre um erro.|
| `idle`              | <p>Acionado após o último quadro renderizado antes do mapa entrar em um estado "ocioso":<ul><li>Nenhuma transição de câmera está em andamento.</li><li>Todos os blocos atualmente solicitados foram carregados.</li><li>Todas as animações de esmaecimento/transição foram concluídas.</li></ul></p>|
| `keydown`           | Acionado quando uma tecla é pressionada.|
| `keypress`          | Acionado quando uma chave que produz um caractere typable (uma tecla ANSI) é pressionada.|
| `keyup`             | Acionado quando uma chave é liberada.|
| `layeradded`        | Acionado quando uma camada é adicionada ao mapa.|
| `layerremoved`      | Acionado quando uma camada é removida do mapa.|
| `load`              | Acionado imediatamente depois que todos os recursos necessários tiverem sido baixados e o primeiro processamento visual completo do mapa tiver ocorrido.|
| `mousedown`         | Acionado quando um dispositivo apontador é pressionado no mapa ou na parte superior de um elemento.|
| `mouseenter`        | Acionado quando um dispositivo apontador é movido inicialmente sobre o mapa ou um elemento. |
| `mouseleave`        | Acionado quando um dispositivo apontador é movido para fora do mapa ou de um elemento. |
| `mousemove`         | Acionado quando um dispositivo apontador é movido dentro do mapa ou de um elemento.|
| `mouseout`          | Acionado quando um dispositivo de ponto deixa a tela do mapa, nosso deixa um elemento.|
| `mouseover`         | Acionado quando um dispositivo apontador é movido sobre o mapa ou um elemento.|
| `mouseup`           | Acionado quando um dispositivo apontador é liberado no mapa ou na parte superior de um elemento.|
| `move`              | Disparado repetidamente durante uma transição animada de uma exibição para outra, como resultado de uma interação do usuário ou métodos.|
| `moveend`           | Acionado logo após o mapa concluir uma transição de uma exibição para outra, como resultado de uma interação do usuário ou métodos.|
| `movestart`         | Disparado pouco antes de o mapa começar uma transição de uma exibição para outra, como resultado de uma interação do usuário ou métodos.|
| `open`              | Acionado quando o popup é aberto manualmente ou programaticamente.|
| `pitch`             | Acionado sempre que a inclinação do mapa (inclinação) muda como resultado de uma interação do usuário ou métodos.|
| `pitchend`          | Acionado imediatamente após a densidade do mapa (inclinação) terminar de ser alterado como resultado da interação do usuário ou dos métodos.|
| `pitchstart`        | Acionado sempre que a densidade do mapa (inclinação) inicia uma alteração como resultado de uma interação do usuário ou métodos.|
| `ready`             | Acionado quando os recursos de mapa mínimos necessários são carregados antes que o mapa esteja pronto para ser interagindo de forma programática.|
| `render`            | <p>Acionado sempre que o mapa é desenhado na tela, como resultado de:<ul><li>Uma alteração na posição, no zoom, na inclinação ou no posicionamento do mapa.</li><li>Uma alteração no estilo do mapa.</li><li>Uma alteração em uma fonte de `DataSource`.</li><li>O carregamento de um bloco de vetor, arquivo geojson, glifo ou Sprite.</li></ul></p>|
| `resize`            | Acionado imediatamente após o mapa ter sido redimensionado.|
| `rotate`            | Acionado repetidamente durante uma interação "arrastar para girar".|
| `rotateend`         | Acionado quando uma interação "arrastar para girar" termina.|
| `rotatestart`       | Acionado quando uma interação "arrastar para girar" é iniciada.|
| `shapechanged`      | Acionado quando uma propriedade de objeto de forma é alterada.|
| `sourcedata`        | Acionado quando uma das fontes do mapa é carregada ou alterada, incluindo se um bloco que pertence a uma origem carrega ou altera. |
| `sourceadded`       | Acionado quando um `DataSource` ou `VectorTileSource` é adicionado ao mapa.|
| `sourceremoved`     | Acionado quando um `DataSource` ou `VectorTileSource` é removido do mapa.|
| `styledata`         | Acionado quando o estilo do mapa é carregado ou alterado.|
| `styleimagemissing` | Acionado quando uma camada tenta carregar uma imagem da imagem Sprite que não existe |
| `tokenacquired`     | Acionado quando um token de acesso do AAD é obtido.|
| `touchcancel`       | Acionado quando um evento touchcancel ocorre dentro do mapa.|
| `touchend`          | Acionado quando um evento touchend ocorre dentro do mapa.|
| `touchmove`         | Acionado quando um evento TouchMove ocorre dentro do mapa.|
| `touchstart`        | Acionado quando um evento touchstart ocorre dentro do mapa.|
| `wheel`             | Acionado quando ocorre um evento de roda do mouse dentro do mapa.|
| `zoom`              | Disparado repetidamente durante uma transição animada de um nível de zoom para outro, como resultado de uma interação do usuário ou métodos.|
| `zoomend`           | Disparado logo após o mapa concluir uma transição de um nível de zoom para outro, como resultado de uma interação do usuário ou métodos.|
| `zoomstart`         | Disparado pouco antes de o mapa começar uma transição de um nível de zoom para outro, como resultado de uma interação do usuário ou métodos.|


## <a name="next-steps"></a>Passos seguintes

Consulte os seguintes artigos para obter exemplos de código completo:

> [!div class="nextstepaction"]
> [Usando o módulo de serviços do Azure Maps](./how-to-use-services-module.md)

> [!div class="nextstepaction"]
> [Exemplos de código](https://docs.microsoft.com/samples/browse/?products=azure-maps)
