---
title: Lidar com eventos de mapas | Microsoft Azure Maps
description: Saiba quais os eventos que são disparados quando os utilizadores interagem com mapas. Veja uma lista de todos os eventos de mapas suportados. Veja como usar o Azure Maps Web SDK para lidar com eventos.
author: anastasia-ms
ms.author: v-stharr
ms.date: 09/10/2019
ms.topic: conceptual
ms.service: azure-maps
services: azure-maps
manager: ''
ms.custom: codepen, devx-track-js
ms.openlocfilehash: 8121ceb68bdea9332316a9508bf6f4731e05b0ba
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/19/2021
ms.locfileid: "92890790"
---
# <a name="interact-with-the-map"></a>Interaja com o mapa

Este artigo mostra-lhe como usar a [classe de eventos de mapas.](/javascript/api/azure-maps-control/atlas.map#events) A propriedade destaca eventos no mapa e em diferentes camadas do mapa. Também pode destacar eventos quando interage com um marcador HTML.

## <a name="interact-with-the-map"></a>Interaja com o mapa

Jogue com o mapa abaixo e veja os eventos correspondentes do rato realçados à direita. Pode clicar no **separador JS** para visualizar e editar o código JavaScript. Também pode clicar em **Editar no CodePen** para modificar o código no CodePen.

<br/>

<iframe height='600' scrolling='no' title='Interagindo com o mapa - eventos de rato' src='//codepen.io/azuremaps/embed/bLZEWd/?height=600&theme-id=0&default-tab=js,result&embed-version=2&editable=true' frameborder='no' loading="lazy" allowtransparency='true' allowfullscreen='true' style='width: 100%;'>Consulte a Pen <a href='https://codepen.io/azuremaps/pen/bLZEWd/'>Interact com o mapa – eventos de rato</a> por Azure Maps ( ) no <a href='https://codepen.io/azuremaps'>@azuremaps</a> <a href='https://codepen.io'>CodePen</a>.
</iframe>

## <a name="interact-with-map-layers"></a>Interaja com as camadas de mapa

O código seguinte realça o evento disparado à medida que interage com a Camada de Símbolo. O símbolo, a bolha, a linha e a camada de polígono suportam todos o mesmo conjunto de eventos. O mapa de calor e as camadas de azulejos não suportam nenhum destes eventos.

<br/>

<iframe height='600' scrolling='no' title='Interagindo com o mapa - Eventos de Camada' src='//codepen.io/azuremaps/embed/bQRRPE/?height=600&theme-id=0&default-tab=js,result&embed-version=2&editable=true' frameborder='no' loading="lazy" allowtransparency='true' allowfullscreen='true' style='width: 100%;'>Consulte a Pen <a href='https://codepen.io/azuremaps/pen/bQRRPE/'>Interagindo com o mapa – Layer Events</a> by Azure Maps <a href='https://codepen.io/azuremaps'>@azuremaps</a> () no <a href='https://codepen.io'>CodePen</a>.
</iframe>

## <a name="interact-with-html-marker"></a>Interaja com o marcador HTML

O código seguinte adiciona eventos de mapa JavaScript a um marcador HTML. Também destaca o nome dos eventos que são disparados à medida que interage com o marcador HTML.

<br/>

<iframe height='500' scrolling='no' title='Interagindo com o mapa - eventos de marcador HTML' src='//codepen.io/azuremaps/embed/VVzKJY/?height=500&theme-id=0&default-tab=js,result&embed-version=2&editable=true' frameborder='no' loading="lazy" allowtransparency='true' allowfullscreen='true' style='width: 100%;'>Consulte a Pen <a href='https://codepen.io/azuremaps/pen/VVzKJY/'>Interacting com o mapa - HTML Marker events</a> by Azure Maps ( ) em <a href='https://codepen.io/azuremaps'>@azuremaps</a> <a href='https://codepen.io'>CodePen</a>.
</iframe>

A tabela que se segue lista todos os eventos de classe de mapas suportados.

| Evento               | Description |
|---------------------|-------------|
| `boxzoomend`        | Disparado quando termina uma interação de "zoom de caixa".|
| `boxzoomstart`      | Disparado quando uma interação de "zoom de caixa" começa.|
| `click`             | Disparado quando um dispositivo de apontamento é pressionado e libertado no mesmo ponto do mapa.|
| `close`             | Disparado quando o pop-up estiver fechado manualmente ou programaticamente.|
| `contextmenu`       | Disparado quando o botão direito do rato é clicado.|
| `data`              | Disparado quando qualquer dado do mapa carrega ou muda. |
| `dataadded`         | Disparado quando as formas são adicionadas ao `DataSource` .|
| `dataremoved`       | Disparado quando as formas são removidas do `DataSource` .|
| `datasourceupdated` | Disparado quando o `DataSource` objeto for atualizado.|
| `dblclick`          | Disparado quando um dispositivo de apontamento é clicado duas vezes no mesmo ponto do mapa.|
| `drag`              | Disparado repetidamente durante uma interação "arrastar para pan" no mapa, popup ou marcador HTML.|
| `dragend`           | Disparado quando uma interação "arrastar para pan" termina no mapa, popup ou marcador HTML.|
| `dragstart`         | Disparado quando uma interação "arrastar para pan" começa no mapa, popup ou marcador HTML.|
| `error`             | Disparado quando ocorre um erro.|
| `idle`              | <p>Disparado após o último quadro prestado antes do mapa entrar num estado "ocioso":<ul><li>Não há transições de câmaras em andamento.</li><li>Todos os azulejos atualmente solicitados carregaram.</li><li>Todas as animações de fade/transição completaram.</li></ul></p>|
| `keydown`           | Disparado quando uma chave é pressionada para baixo.|
| `keypress`          | Disparado quando uma tecla que produz um carácter tipível (uma tecla ANSI) é pressionada.|
| `keyup`             | Disparado quando uma chave é libertada.|
| `layeradded`        | Disparado quando uma camada é adicionada ao mapa.|
| `layerremoved`      | Disparado quando uma camada é removida do mapa.|
| `load`              | Disparado imediatamente após todo o necessário recursos ter sido descarregado e ocorreu a primeira renderização visualmente completa do mapa.|
| `mousedown`         | Disparado quando um dispositivo de apontamento é pressionado dentro do mapa ou quando em cima de um elemento.|
| `mouseenter`        | Disparado quando um dispositivo de apontamento é inicialmente movido sobre o mapa ou um elemento. |
| `mouseleave`        | Disparado quando um dispositivo de apontamento é movido para fora do mapa ou um elemento. |
| `mousemove`         | Disparado quando um dispositivo de apontamento é movido dentro do mapa ou de um elemento.|
| `mouseout`          | Disparado quando um dispositivo de ponto sai da tela do mapa, deixamos um elemento.|
| `mouseover`         | Disparado quando um dispositivo de apontamento é movido sobre o mapa ou um elemento.|
| `mouseup`           | Disparado quando um dispositivo de apontamento é lançado dentro do mapa ou quando em cima de um elemento.|
| `move`              | Disparado repetidamente durante uma transição animada de uma vista para outra, como resultado da interação ou métodos do utilizador.|
| `moveend`           | Disparado logo após o mapa completar uma transição de uma vista para outra, como resultado da interação ou métodos do utilizador.|
| `movestart`         | Disparado pouco antes do mapa iniciar uma transição de uma vista para outra, como resultado da interação ou métodos do utilizador.|
| `open`              | Disparado quando o pop-up é aberto manualmente ou programaticamente.|
| `pitch`             | Disparado sempre que o tom do mapa (inclinação) se alterar como resultado da interação ou métodos do utilizador.|
| `pitchend`          | Disparado imediatamente após o pitch (inclinação) do mapa terminar a mudança como resultado da interação do utilizador ou dos métodos.|
| `pitchstart`        | Disparado sempre que o tom do mapa (inclinação) começa a ser alterado como resultado da interação ou métodos do utilizador.|
| `ready`             | Disparado quando os recursos mínimos necessários do mapa são carregados antes que o mapa esteja pronto para ser interagido programáticamente com.|
| `render`            | <p>Disparado sempre que o mapa é desenhado para o ecrã, como resultado de:<ul><li>Uma mudança na posição do mapa, zoom, pitch ou rolamento.</li><li>Uma mudança no estilo do mapa.</li><li>Uma mudança para uma `DataSource` fonte.</li><li>O carregamento de um azulejo vetorial, arquivo GeoJSON, glifo ou sprite.</li></ul></p>|
| `resize`            | Disparado imediatamente após o mapa ter sido redimensionado.|
| `rotate`            | Disparado repetidamente durante uma interação de "arrastar para rodar".|
| `rotateend`         | Disparado quando termina uma interação de "arrastar para rodar".|
| `rotatestart`       | Disparado quando começa uma interação de "arrastar para rodar".|
| `shapechanged`      | Disparado quando uma propriedade de objeto de forma é alterada.|
| `sourcedata`        | Disparado quando uma das fontes do mapa carrega ou muda, incluindo se um azulejo pertencente a uma fonte carrega ou muda. |
| `sourceadded`       | Disparado quando um `DataSource` ou é adicionado ao `VectorTileSource` mapa.|
| `sourceremoved`     | Disparado quando um `DataSource` ou é removido do `VectorTileSource` mapa.|
| `styledata`         | Disparado quando o estilo do mapa é carregado ou muda.|
| `styleimagemissing` | Disparado quando uma camada tenta carregar uma imagem do sprite de imagem que não existe |
| `tokenacquired`     | Disparado quando um token de acesso do Diretório Ativo Azure é obtido.|
| `touchcancel`       | Disparado quando um `touchcancel` evento ocorre dentro do mapa.|
| `touchend`          | Disparado quando um `touchend` evento ocorre dentro do mapa.|
| `touchmove`         | Disparado quando um `touchmove` evento ocorre dentro do mapa.|
| `touchstart`        | Disparado quando um `touchstart` evento ocorre dentro do mapa.|
| `wheel`             | Disparado quando ocorre um evento de roda de rato dentro do mapa.|
| `zoom`              | Disparado repetidamente durante uma transição animada de um nível de zoom para outro, como resultado da interação do utilizador ou dos métodos.|
| `zoomend`           | Disparado logo após o mapa completar uma transição de um nível de zoom para outro, como resultado da interação do utilizador ou dos métodos.|
| `zoomstart`         | Disparado pouco antes do mapa iniciar uma transição de um nível de zoom para outro, como resultado da interação do utilizador ou dos métodos.|


## <a name="next-steps"></a>Passos seguintes

Consulte os seguintes artigos para obter exemplos de código completo:

> [!div class="nextstepaction"]
> [Utilizando o módulo Azure Maps Services](./how-to-use-services-module.md)

> [!div class="nextstepaction"]
> [Amostras de código](/samples/browse/?products=azure-maps)