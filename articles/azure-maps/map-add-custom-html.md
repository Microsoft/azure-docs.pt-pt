---
title: Adicione um marcador HTML para mapear ! Microsoft Azure Maps
description: Saiba como adicionar marcadores HTML aos mapas. Veja como usar o Azure Maps Web SDK para personalizar marcadores e adicionar popups e eventos de rato a um marcador.
author: anastasia-ms
ms.author: v-stharr
ms.date: 07/29/2019
ms.topic: conceptual
ms.service: azure-maps
services: azure-maps
manager: ''
ms.custom: codepen, devx-track-js
ms.openlocfilehash: 25ed41be53c114c258e5c63437ff2b5df875af75
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/09/2020
ms.locfileid: "91310735"
---
# <a name="add-html-markers-to-the-map"></a>Adicione marcadores HTML ao mapa

Este artigo mostra-lhe como adicionar um HTML personalizado, como um ficheiro de imagem no mapa como um marcador HTML.

> [!NOTE]
> Os marcadores HTML não se ligam a fontes de dados. Em vez disso, a informação de posição é adicionada diretamente ao marcador e o marcador é adicionado à propriedade do mapa `markers` que é um [HtmlMarkerManager](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.htmlmarkermanager).

> [!IMPORTANT]
> Ao contrário da maioria das camadas no controlo Web Azure Maps que utilizam o WebGL para renderização, os marcadores HTML utilizam elementos doM tradicionais para renderização. Como tal, quanto mais marcadores HTML adicionados a uma página, mais elementos DOM existem. O desempenho pode degradar-se depois de adicionar algumas centenas de marcadores HTML. Para conjuntos de dados maiores considere o agrupamento dos seus dados ou a utilização de uma camada de Símbolo ou Bolha.

## <a name="add-an-html-marker"></a>Adicione um marcador HTML

A classe [HtmlMarker](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.htmlmarker) tem um estilo predefinido. Pode personalizar o marcador definindo as opções de cor e texto do marcador. O estilo padrão da classe marcador HTML é um modelo SVG que tem um `{color}` `{text}` espaço reservado. Desa estarde as propriedades de cor e texto nas opções do marcador HTML para uma rápida personalização. 

O código a seguir cria um marcador HTML e define a propriedade de cor para "DodgerBlue" e a propriedade de texto para "10". Um popup é ligado ao marcador e o `click` evento é usado para alternar a visibilidade do popup.

```javascript
//Create an HTML marker and add it to the map.
var marker = new atlas.HtmlMarker({
    color: 'DodgerBlue',
    text: '10',
    position: [0, 0],
    popup: new atlas.Popup({
        content: '<div style="padding:10px">Hello World</div>',
        pixelOffset: [0, -30]
    })
});

map.markers.add(marker);

//Add a click event to toggle the popup.
map.events.add('click',marker, () => {
    marker.togglePopup();
});
```

Abaixo está a amostra completa do código de execução da funcionalidade acima.

<br/>

<iframe height='500' scrolling='no' title='Adicione um marcador HTML a um mapa' src='//codepen.io/azuremaps/embed/MVoeVw/?height=500&theme-id=0&default-tab=js,result&embed-version=2&editable=true' frameborder='no' loading="lazy" allowtransparency='true' allowfullscreen='true' style='width: 100%;'>Consulte a caneta <a href='https://codepen.io/azuremaps/pen/MVoeVw/'>Adicione um marcador HTML a um mapa</a> de Azure Maps <a href='https://codepen.io/azuremaps'>@azuremaps</a> () em <a href='https://codepen.io'>CodePen</a>.
</iframe>

## <a name="create-svg-templated-html-marker"></a>Criar marcador HTML modelo SVG

O padrão `htmlContent` de um marcador Html é um modelo SVG com pastas de lugar e `{color}` `{text}` nele. Pode criar cadeias SVG personalizadas e adicionar estes mesmos espaços reservados ao seu SVG de modo a definir as `color` opções e `text` opções do marcador atualizar estes espaços reservados no seu SVG.

<br/>

<iframe height='500' scrolling='no' title='Marcador HTML com modelo SVG personalizado' src='//codepen.io/azuremaps/embed/LXqMWx/?height=500&theme-id=0&default-tab=js,result&embed-version=2&editable=true' frameborder='no' loading="lazy" allowtransparency='true' allowfullscreen='true' style='width: 100%;'>Consulte o marcador pen <a href='https://codepen.io/azuremaps/pen/LXqMWx/'>HTML com modelo SVG personalizado</a> por Azure Maps <a href='https://codepen.io/azuremaps'>@azuremaps</a> () em <a href='https://codepen.io'>CodePen</a>.
</iframe>

> [!TIP]
> A Azure Maps web SDK fornece vários modelos de imagem SVG que podem ser usados com marcadores HTML. Para obter mais informações, consulte o documento [como usar modelos de imagem.](how-to-use-image-templates-web-sdk.md)

## <a name="add-a-css-styled-html-marker"></a>Adicione um marcador HTML estilo CSS

Um dos benefícios dos marcadores HTML é que existem muitas grandes personalizações que podem ser alcançadas usando CSS. Nesta amostra, o conteúdo do HtmlMarker é composto por HTML e CSS que criam um pino animado que cai no lugar e pulsa.

<br/>

<iframe height='500' scrolling='no' title='Fonte de dados HTML' src='//codepen.io/azuremaps/embed/qJVgMx/?height=500&theme-id=0&default-tab=js,result&embed-version=2&editable=true' frameborder='no' loading="lazy" allowtransparency='true' allowfullscreen='true' style='width: 100%;'>Consulte a Pen <a href='https://codepen.io/azuremaps/pen/qJVgMx/'>HTML DataSource</a> by Azure Maps <a href='https://codepen.io/azuremaps'>@azuremaps</a> () no <a href='https://codepen.io'>CodePen</a>.
</iframe>

## <a name="draggable-html-markers"></a>Marcadores HTML arrastados

Esta amostra mostra como fazer um marcador HTML arrastado. Suporte de marcadores HTML `drag` `dragstart` , e `dragend` eventos.

<br/>

<iframe height='500' scrolling='no' title='Marcador HTML arrastado' src='//codepen.io/azuremaps/embed/wQZoEV/?height=500&theme-id=0&default-tab=js,result&embed-version=2&editable=true' frameborder='no' loading="lazy" allowtransparency='true' allowfullscreen='true' style='width: 100%;'>Consulte o marcador HTML de Caneta <a href='https://codepen.io/azuremaps/pen/wQZoEV/'>Draggable</a> html por Azure Maps <a href='https://codepen.io/azuremaps'>@azuremaps</a> () no <a href='https://codepen.io'>CodePen</a>.
</iframe>

## <a name="add-mouse-events-to-html-markers"></a>Adicione eventos de rato a marcadores HTML

Estas amostras mostram como adicionar eventos de rato e arrastar a um marcador HTML.

<br/>

<iframe height='500' scrolling='no' title='Adicionar eventos de rato a marcadores HTML' src='//codepen.io/azuremaps/embed/RqOKRz/?height=500&theme-id=0&default-tab=js,result&embed-version=2&editable=true' frameborder='no' loading="lazy" allowtransparency='true' allowfullscreen='true' style='width: 100%;'>Consulte os <a href='https://codepen.io/azuremaps/pen/RqOKRz/'>eventos de adicionar rato à HTML Markers</a> by Azure Maps <a href='https://codepen.io/azuremaps'>@azuremaps</a> () em <a href='https://codepen.io'>CodePen</a>.
</iframe>

## <a name="next-steps"></a>Passos seguintes

Saiba mais sobre as aulas e métodos utilizados neste artigo:

> [!div class="nextstepaction"]
> [HtmlMarker](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.htmlmarker)

> [!div class="nextstepaction"]
> [HtmlMarkerOptions](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.htmlmarkeroptions)

> [!div class="nextstepaction"]
> [HtmlMarkerManager](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.htmlmarkermanager)

Para obter mais exemplos de código para adicionar aos seus mapas, consulte os seguintes artigos:

> [!div class="nextstepaction"]
> [Como utilizar modelos de imagem](how-to-use-image-templates-web-sdk.md)

> [!div class="nextstepaction"]
> [Adicionar uma camada de símbolo](./map-add-pin.md)

> [!div class="nextstepaction"]
> [Adicionar uma camada de bolha](./map-add-bubble-layer.md)
