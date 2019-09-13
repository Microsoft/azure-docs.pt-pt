---
title: Manipular eventos com o Azure Maps | Microsoft Docs
description: Como criar um mapa de SDK da Web interativo com eventos de mapa
author: jingjing-z
ms.author: jinzh
ms.date: 09/10/2019
ms.topic: conceptual
ms.service: azure-maps
services: azure-maps
manager: ''
ms.custom: codepen
ms.openlocfilehash: c5b4ed73d7bc4d89a67280a0bb183f374ae093d8
ms.sourcegitcommit: 3e7646d60e0f3d68e4eff246b3c17711fb41eeda
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 09/11/2019
ms.locfileid: "70899437"
---
# <a name="interact-with-the-map"></a>Interagir com o mapa

Este artigo mostra como usar a propriedade de [eventos da classe Map](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.map?#events) para realçar eventos no mapa e em camadas diferentes do mapa. Ele também mostra como usar a propriedade de eventos da classe Map para realçar eventos quando você interage com um marcador HTML.

## <a name="interact-with-the-map"></a>Interagir com o mapa

Jogue com o mapa abaixo e veja os eventos de mouse correspondentes realçados à direita. Você pode clicar na **guia js** para exibir e editar o código JavaScript. Você também pode clicar no botão **Editar no CodePen** e editar o código em CodePen.

<br/>

<iframe height='600' scrolling='no' title='Interagindo com o mapa – eventos de mouse' src='//codepen.io/azuremaps/embed/bLZEWd/?height=600&theme-id=0&default-tab=js,result&embed-version=2&editable=true' frameborder='no' allowtransparency='true' allowfullscreen='true' style='width: 100%;'>Consulte a caneta <a href='https://codepen.io/azuremaps/pen/bLZEWd/'>interagir com o mapa – eventos de mouse</a> por mapas do<a href='https://codepen.io/azuremaps'>@azuremaps</a>Azure () em <a href='https://codepen.io'>CodePen</a>.
</iframe>

## <a name="interact-with-map-layers"></a>Interagir com as camadas do mapa

O código a seguir realça o nome dos eventos que são acionados enquanto você interage com a camada de símbolo. O símbolo, a bolha, a linha e a camada de polígono oferecem suporte ao mesmo conjunto de eventos. As camadas mapa de calor e bloco não oferecem suporte a nenhum desses eventos.

<br/>

<iframe height='600' scrolling='no' title='Interagindo com o mapa – eventos de camada' src='//codepen.io/azuremaps/embed/bQRRPE/?height=600&theme-id=0&default-tab=js,result&embed-version=2&editable=true' frameborder='no' allowtransparency='true' allowfullscreen='true' style='width: 100%;'>Consulte a caneta <a href='https://codepen.io/azuremaps/pen/bQRRPE/'>interagindo com os eventos de camada de mapa</a> por mapas do<a href='https://codepen.io/azuremaps'>@azuremaps</a>Azure () em <a href='https://codepen.io'>CodePen</a>.
</iframe>

## <a name="interact-with-html-marker"></a>Interagir com marcador HTML

O código a seguir adiciona eventos de mapa JavaScript a um marcador HTML. Ele também realça o nome dos eventos que são acionados enquanto você interage com o marcador HTML.

<br/>

<iframe height='500' scrolling='no' title='Interagindo com o mapa-eventos de marcador HTML' src='//codepen.io/azuremaps/embed/VVzKJY/?height=500&theme-id=0&default-tab=js,result&embed-version=2&editable=true' frameborder='no' allowtransparency='true' allowfullscreen='true' style='width: 100%;'>Consulte a caneta <a href='https://codepen.io/azuremaps/pen/VVzKJY/'>interagindo com os eventos do marcador MAP-HTML</a> pelo Azure Maps<a href='https://codepen.io/azuremaps'>@azuremaps</a>() em <a href='https://codepen.io'>CodePen</a>.
</iframe>

A tabela a seguir lista todos os eventos de classe de mapa com suporte.

| Evento             | Descrição |
|-------------------|-------------|
| boxzoomend        | Acionado quando uma interação de "caixa de zoom" termina.|
| boxzoomstart      | Acionado quando uma interação "caixa de zoom" é iniciada.|
| Selecione             | Acionado quando um dispositivo apontador é pressionado e liberado no mesmo ponto no mapa.|
| fechar             | Acionado quando o popup é fechado manualmente ou programaticamente.|
| ContextMenu       | Acionado quando o botão direito do mouse é clicado.|
| adicionado ao         | Acionado quando formas são adicionadas à fonte de fontes.|
| dataremoved       | Acionado quando as formas são removidas da fonte de base.|
| datasourceupdated | Acionado quando o objeto DataSource é atualizado.|
| DblClick          | Acionado quando um dispositivo apontador é clicado duas vezes no mesmo ponto no mapa.|
| Arraste              | Acionado repetidamente durante uma interação "arrastar para panorâmica" no marcador de mapa, pop-up ou HTML.|
| dragend           | Acionado quando uma interação "arrastar para a panorâmica" termina no marcador de mapa, pop-up ou HTML.|
| dragstart         | Acionado quando uma interação "arrastar para a panorâmica" começa no marcador de mapa, pop-up ou HTML.|
| error             | Acionado quando ocorre um erro.|
| KeyDown           | Acionado quando uma tecla é pressionada.|
| pressionar          | Acionado quando uma chave que produz um caractere typable (uma tecla ANSI) é pressionada.|
| KeyUp             | Acionado quando uma chave é liberada.|
| layeradded        | Acionado quando uma camada é adicionada ao mapa.|
| load              | Acionado imediatamente depois que todos os recursos necessários tiverem sido baixados e o primeiro processamento visual completo do mapa tiver ocorrido.|
| MouseDown         | Acionado quando um dispositivo apontador é pressionado no mapa.|
| ocorra         | Acionado quando um dispositivo apontador é movido dentro do mapa.|
| mouseout          | Acionado quando um dispositivo de ponto deixa a tela do mapa.|
| acima         | Acionado quando um dispositivo apontador é movido dentro do mapa.|
| MouseUp           | Acionado quando um dispositivo apontador é liberado dentro do mapa.|
| Prosseguir              | Disparado repetidamente durante uma transição animada de uma exibição para outra, como resultado de uma interação do usuário ou métodos.|
| moveend           | Acionado logo após o mapa concluir uma transição de uma exibição para outra, como resultado de uma interação do usuário ou métodos.|
| movestart         | Disparado pouco antes de o mapa começar uma transição de uma exibição para outra, como resultado de uma interação do usuário ou métodos.|
| abre              | Acionado quando o popup é aberto manualmente ou programaticamente.|
| zumbi             | Acionado sempre que a inclinação do mapa (inclinação) muda como resultado de uma interação do usuário ou métodos.|
| pitchend          | Acionado imediatamente após a densidade do mapa (inclinação) terminar de ser alterado como resultado da interação do usuário ou dos métodos.|
| pitchstart        | Acionado sempre que a densidade do mapa (inclinação) inicia uma alteração como resultado de uma interação do usuário ou métodos.|
| preparado             | Acionado quando os recursos de mapa mínimos necessários são carregados antes que o mapa esteja pronto para ser interagindo de forma programática.|
| aplicar            | <p> Acionado sempre que o mapa é desenhado na tela, como resultado de:<ul><li>Uma alteração na posição, no zoom, na inclinação ou no posicionamento do mapa.</li><li>Uma alteração no estilo do mapa.</li><li>Uma alteração em uma fonte DataSource.</li><li>O carregamento de um bloco de vetor, arquivo geojson, glifo ou Sprite.</li></ul></p>|
| Alonga            | Acionado imediatamente após o mapa ter sido redimensionado.|
| girar            | Acionado repetidamente durante uma interação "arrastar para girar".|
| rotateend         | Acionado quando uma interação "arrastar para girar" termina.|
| rotatestart       | Acionado quando uma interação "arrastar para girar" é iniciada.|
| formachanged      | Acionado quando uma propriedade de objeto de forma é alterada.|
| sourceadded       | Acionado quando uma DataSource ou VectorTileSource é adicionada ao mapa.|
| sourceremoved     | Acionado quando uma DataSource ou VectorTileSource é removida do mapa.|
| styledata         | Acionado quando o estilo do mapa é carregado ou alterado.|
| tokenacquired     | Acionado quando um token de acesso do AAD é obtido.|
| touchcancel       | Acionado quando um evento touchcancel ocorre dentro do mapa.|
| touchend          | Acionado quando um evento touchend ocorre dentro do mapa.|
| touchmove         | Acionado quando um evento TouchMove ocorre dentro do mapa.|
| touchstart        | Acionado quando um evento touchstart ocorre dentro do mapa.|
| mecanismo             | Acionado quando ocorre um evento de roda do mouse dentro do mapa.|
| Aplicar              | Disparado repetidamente durante uma transição animada de um nível de zoom para outro, como resultado de uma interação do usuário ou métodos.|
| zoomend           | Disparado logo após o mapa concluir uma transição de um nível de zoom para outro, como resultado de uma interação do usuário ou métodos.|
| zoomstart         | Disparado pouco antes de o mapa começar uma transição de um nível de zoom para outro, como resultado de uma interação do usuário ou métodos.|


## <a name="next-steps"></a>Passos seguintes

Consulte os seguintes artigos para obter exemplos de código completo:

> [!div class="nextstepaction"]
> [Usando o módulo de serviços do Azure Maps](./how-to-use-services-module.md)

> [!div class="nextstepaction"]
> [Exemplos de código](https://docs.microsoft.com/samples/browse/?products=azure-maps)
