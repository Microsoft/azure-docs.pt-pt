---
title: Lidar com eventos de mapas [ Microsoft Azure Maps
description: Neste artigo, você aprenderá a fazer um mapa Web SDK interativo com eventos de mapas usando o Web SDK do Microsoft Azure Maps.
author: jinzh-azureiot
ms.author: jinzh
ms.date: 09/10/2019
ms.topic: conceptual
ms.service: azure-maps
services: azure-maps
manager: ''
ms.custom: codepen
ms.openlocfilehash: b97371d1b63ad4abfe1635e426df1449ab5f3f14
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/28/2020
ms.locfileid: "79534903"
---
# <a name="interact-with-the-map"></a>Interaja com o mapa

Este artigo mostra-lhe como usar a classe de [eventos de mapas.](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.map?#events) A propriedade destaca eventos no mapa e em diferentes camadas do mapa. Também pode destacar eventos quando interage com um marcador HTML.

## <a name="interact-with-the-map"></a>Interaja com o mapa

Jogue com o mapa abaixo e veja os eventos correspondentes do rato realçados à direita. Pode clicar no **separador JS** para visualizar e editar o código JavaScript. Também pode clicar em **Editar no CodePen** para modificar o código no CodePen.

<br/>

<iframe height='600' scrolling='no' title='Interagindo com o mapa – eventos de rato' src='//codepen.io/azuremaps/embed/bLZEWd/?height=600&theme-id=0&default-tab=js,result&embed-version=2&editable=true' frameborder='no' allowtransparency='true' allowfullscreen='true' style='width: 100%;'>Veja a Caneta <a href='https://codepen.io/azuremaps/pen/bLZEWd/'>Interagir com o mapa – eventos</a> de rato por Azure Maps (<a href='https://codepen.io/azuremaps'>@azuremaps</a>) em <a href='https://codepen.io'>CodePen</a>.
</iframe>

## <a name="interact-with-map-layers"></a>Interaja com camadas de mapa

O código seguinte destaca o evento disparado enquanto interage com a Camada de Símbolo. O símbolo, bolha, linha e camada de polígono suportam o mesmo conjunto de eventos. O mapa de calor e as camadas de azulejos não suportam nenhum destes eventos.

<br/>

<iframe height='600' scrolling='no' title='Interagindo com o mapa – Eventos de Camadas' src='//codepen.io/azuremaps/embed/bQRRPE/?height=600&theme-id=0&default-tab=js,result&embed-version=2&editable=true' frameborder='no' allowtransparency='true' allowfullscreen='true' style='width: 100%;'>Veja a Caneta <a href='https://codepen.io/azuremaps/pen/bQRRPE/'>Interagindo com o mapa – Eventos de Camadas</a> por Azure Maps ()<a href='https://codepen.io/azuremaps'>@azuremaps</a>em <a href='https://codepen.io'>CodePen</a>.
</iframe>

## <a name="interact-with-html-marker"></a>Interaja com o marcador HTML

O código seguinte adiciona eventos de mapas Javascript a um marcador HTML. Também destaca o nome dos eventos que são disparados à medida que interage com o marcador HTML.

<br/>

<iframe height='500' scrolling='no' title='Interagindo com o mapa - Eventos de marcador HTML' src='//codepen.io/azuremaps/embed/VVzKJY/?height=500&theme-id=0&default-tab=js,result&embed-version=2&editable=true' frameborder='no' allowtransparency='true' allowfullscreen='true' style='width: 100%;'>Veja a Caneta <a href='https://codepen.io/azuremaps/pen/VVzKJY/'>Interagindo com o mapa - EVENTOS DE Marcador HTML</a> por Azure Maps ()<a href='https://codepen.io/azuremaps'>@azuremaps</a>em <a href='https://codepen.io'>CodePen</a>.
</iframe>

A tabela seguinte lista todos os eventos de classe de mapas suportados.

| Evento               | Descrição |
|---------------------|-------------|
| `boxzoomend`        | Disparado quando termina uma interação de "zoom de caixa".|
| `boxzoomstart`      | Disparado quando começa uma interação de "zoom de caixa".|
| `click`             | Disparado quando um dispositivo de ponta é pressionado e libertado no mesmo ponto do mapa.|
| `close`             | Disparado quando o pop-up é fechado manualmente ou programaticamente.|
| `contextmenu`       | Disparado quando o botão certo do rato é clicado.|
| `data`              | Disparado quando qualquer mapa de dados carrega ou muda. |
| `dataadded`         | Disparado quando as formas `DataSource`são adicionadas ao .|
| `dataremoved`       | Disparado quando as formas `DataSource`são removidas do .|
| `datasourceupdated` | Disparado quando `DataSource` o objeto é atualizado.|
| `dblclick`          | Disparado quando um dispositivo de pontaé clicado duas vezes no mesmo ponto do mapa.|
| `drag`              | Disparado repetidamente durante uma interação "arrastar para pan" no mapa, popup ou marcador HTML.|
| `dragend`           | Disparado quando uma interação "arrastar para a panela" termina no mapa, popup ou marcador HTML.|
| `dragstart`         | Disparado quando uma interação "arrastar para a panela" começa no mapa, popup ou marcador HTML.|
| `error`             | Disparado quando ocorre um erro.|
| `idle`              | <p>Disparado após a última moldura prestada antes do mapa entrar num estado "inativo":<ul><li>Não estão a decorrer transições de câmaras.</li><li>Todos os azulejos atualmente solicitados carregaram.</li><li>Todas as animações de desvanecimento/transição terminaram.</li></ul></p>|
| `keydown`           | Disparada quando uma chave é pressionada para baixo.|
| `keypress`          | Disparada quando é premida uma chave que produz um carácter tipível (uma tecla ANSI).|
| `keyup`             | Disparada quando uma chave é libertada.|
| `layeradded`        | Disparado quando uma camada é adicionada ao mapa.|
| `layerremoved`      | Disparado quando uma camada é removida do mapa.|
| `load`              | Disparado imediatamente após todos os recursos necessários terem sido descarregados e a primeira renderização visualmente completa do mapa ocorreu.|
| `mousedown`         | Disparado quando um dispositivo de ponta é pressionado dentro do mapa ou quando em cima de um elemento.|
| `mouseenter`        | Disparado quando um dispositivo de ponta é inicialmente movido sobre o mapa ou um elemento. |
| `mouseleave`        | Disparado quando um dispositivo de ponta é movido para fora do mapa ou um elemento. |
| `mousemove`         | Disparado quando um dispositivo de ponta é movido dentro do mapa ou de um elemento.|
| `mouseout`          | Disparado quando um dispositivo de ponto deixa a tela do mapa, o nosso deixa um elemento.|
| `mouseover`         | Disparado quando um dispositivo de ponta é movido sobre o mapa ou um elemento.|
| `mouseup`           | Disparado quando um dispositivo de ponta é libertado dentro do mapa ou quando em cima de um elemento.|
| `move`              | Disparado repetidamente durante uma transição animada de uma vista para outra, como resultado da interação do utilizador ou dos métodos.|
| `moveend`           | Disparado logo após o mapa completar uma transição de uma vista para outra, como resultado de uma interação ou métodos do utilizador.|
| `movestart`         | Disparado pouco antes do mapa iniciar uma transição de uma vista para outra, como resultado de uma interação ou métodos do utilizador.|
| `open`              | Disparado quando o pop-up é aberto manualmente ou programaticamente.|
| `pitch`             | Disparado sempre que o tom (inclinação) do mapa muda como resultado da interação ou métodos do utilizador.|
| `pitchend`          | Disparado imediatamente após o pitch (inclinação) do mapa termina a mudança como resultado de uma interação ou métodos do utilizador.|
| `pitchstart`        | Disparado sempre que o tom do mapa (inclinação) inicia uma mudança como resultado de uma interação ou métodos do utilizador.|
| `ready`             | Disparado quando os recursos mínimos necessários são carregados antes que o mapa esteja pronto para ser interagido programáticamente com.|
| `render`            | <p>Disparado sempre que o mapa é desenhado para o ecrã, como resultado de:<ul><li>Uma mudança na posição do mapa, zoom, pitch ou rolamento.</li><li>Uma mudança no estilo do mapa.</li><li>Uma mudança `DataSource` para uma fonte.</li><li>O carregamento de um azulejo vetorial, arquivo GeoJSON, glifo ou sprite.</li></ul></p>|
| `resize`            | Disparado imediatamente após o mapa ter sido redimensionado.|
| `rotate`            | Disparado repetidamente durante uma interação "arrastar para rodar".|
| `rotateend`         | Disparado quando termina uma interação "arrastar para rodar".|
| `rotatestart`       | Disparado quando começa uma interação "arrastar para rodar".|
| `shapechanged`      | Disparado quando uma propriedade de objeto de forma é alterada.|
| `sourcedata`        | Disparado quando uma das fontes do mapa carrega ou muda, incluindo se um azulejo pertencente a uma fonte carrega ou muda. |
| `sourceadded`       | Disparado quando `DataSource` `VectorTileSource` a ou é adicionado ao mapa.|
| `sourceremoved`     | Disparado quando `DataSource` `VectorTileSource` a ou é removido do mapa.|
| `styledata`         | Disparado quando o estilo do mapa carrega ou muda.|
| `styleimagemissing` | Disparado quando uma camada tenta carregar uma imagem do sprite de imagem que não existe |
| `tokenacquired`     | Disparado quando um sinal de acesso AAD é obtido.|
| `touchcancel`       | Disparado quando ocorre um evento de cancelamento de toque dentro do mapa.|
| `touchend`          | Disparado quando ocorre um evento de toque dentro do mapa.|
| `touchmove`         | Disparado quando ocorre um evento touchmove dentro do mapa.|
| `touchstart`        | Disparado quando um evento touchstart ocorre dentro do mapa.|
| `wheel`             | Disparado quando ocorre um evento de roda de rato dentro do mapa.|
| `zoom`              | Disparado repetidamente durante uma transição animada de um nível de zoom para outro, como resultado de uma interação ou métodos do utilizador.|
| `zoomend`           | Disparado logo após o mapa completar uma transição de um nível de zoom para outro, como resultado de uma interação ou métodos do utilizador.|
| `zoomstart`         | Disparado pouco antes do mapa iniciar uma transição de um nível de zoom para outro, como resultado de uma interação ou métodos do utilizador.|


## <a name="next-steps"></a>Passos seguintes

Consulte os seguintes artigos para obter exemplos de código completo:

> [!div class="nextstepaction"]
> [Utilização do módulo Deserviços Azure Maps](./how-to-use-services-module.md)

> [!div class="nextstepaction"]
> [Amostras de código](https://docs.microsoft.com/samples/browse/?products=azure-maps)
