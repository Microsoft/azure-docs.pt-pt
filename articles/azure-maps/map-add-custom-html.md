---
title: Adicionar um marcador HTML ao mapa | Mapas do Microsoft Azure
description: Neste artigo, você aprenderá a adicionar um marcador HTML a um mapa usando o SDK da Web do Microsoft Azure Maps.
author: jingjing-z
ms.author: jinzh
ms.date: 07/29/2019
ms.topic: conceptual
ms.service: azure-maps
services: azure-maps
manager: ''
ms.custom: codepen
ms.openlocfilehash: 5a15b3b9e51772d8c7e5312968fecb180069e2a7
ms.sourcegitcommit: f9601bbccddfccddb6f577d6febf7b2b12988911
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 01/12/2020
ms.locfileid: "75911254"
---
# <a name="add-html-markers-to-the-map"></a>Adicionar marcadores HTML ao mapa

Este artigo mostra como adicionar um HTML personalizado, como um arquivo de imagem, ao mapa como um marcador HTML.

> [!NOTE]
> Os marcadores HTML não se conectam a fontes de dados. Em vez disso, as informações de posição são adicionadas diretamente ao marcador e o marcador é adicionado à propriedade mapas `markers`, que é um [HtmlMarkerManager](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.htmlmarkermanager?view=azure-iot-typescript-latest).

> [!IMPORTANT]
> Ao contrário da maioria das camadas no controle da Web do Azure Maps que usam WebGL para renderização, os marcadores de HTML usam elementos DOM tradicionais para renderização. Assim, quanto mais marcadores HTML adicionavam uma página, mais elementos DOM existem. O desempenho pode diminuir depois de adicionar algumas centenas de marcadores de HTML. Para conjuntos de dados maiores, considere o clustering dos dados ou o uso de um símbolo ou camada de bolha.

## <a name="add-an-html-marker"></a>Adicionar um marcador HTML

A classe [HtmlMarker](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.htmlmarker?view=azure-iot-typescript-latest) tem um estilo padrão. Você pode personalizar o marcador definindo as opções de cor e texto do marcador. O estilo padrão da classe de marcador HTML é um modelo SVG que tem um espaço reservado `{color}` e `{text}`. Defina as propriedades de cor e texto nas opções de marcador HTML para uma personalização rápida. 

O código a seguir cria um marcador HTML e define a Propriedade Color como "DodgerBlue" e a propriedade Text como "10". Um popup é anexado ao marcador e `click` evento é usado para alternar a visibilidade do Popup.

```javascript
//Create a HTML marker and add it to the map.
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

Abaixo está o exemplo de código completo em execução da funcionalidade acima.

<br/>

<iframe height='500' scrolling='no' title='Adicionar um marcador HTML a um mapa' src='//codepen.io/azuremaps/embed/MVoeVw/?height=500&theme-id=0&default-tab=js,result&embed-version=2&editable=true' frameborder='no' allowtransparency='true' allowfullscreen='true' style='width: 100%;'>Consulte a caneta <a href='https://codepen.io/azuremaps/pen/MVoeVw/'>Adicionar um marcador HTML a um mapa</a> pelo Azure Maps (<a href='https://codepen.io/azuremaps'>@azuremaps</a>) em <a href='https://codepen.io'>CodePen</a>.
</iframe>

## <a name="create-svg-templated-html-marker"></a>Criar marcador HTML modelo SVG

O `htmlContent` padrão de um marcador HTML é um modelo SVG com pastas de local `{color}` e `{text}` nele. Você pode criar cadeias de caracteres SVG personalizadas e adicionar esses mesmos espaços reservados ao seu SVG, de modo que a definição das opções `color` e `text` do marcador atualize esses espaços reservados no seu SVG.

<br/>

<iframe height='500' scrolling='no' title='Marcador HTML com modelo SVG personalizado' src='//codepen.io/azuremaps/embed/LXqMWx/?height=500&theme-id=0&default-tab=js,result&embed-version=2&editable=true' frameborder='no' allowtransparency='true' allowfullscreen='true' style='width: 100%;'>Consulte o <a href='https://codepen.io/azuremaps/pen/LXqMWx/'>marcador HTML de caneta com modelo SVG personalizado</a> pelo mapas do Azure (<a href='https://codepen.io/azuremaps'>@azuremaps</a>) em <a href='https://codepen.io'>CodePen</a>.
</iframe>

> [!TIP]
> O SDK da Web do Azure Maps fornece vários modelos de imagem SVG que podem ser usados com marcadores de HTML. Para obter mais informações, consulte o documento [como usar modelos de imagem](how-to-use-image-templates-web-sdk.md) .

## <a name="add-a-css-styled-html-marker"></a>Adicionar um marcador HTML com estilo CSS

Um dos benefícios dos marcadores HTML é que há muitas ótimas personalizações que podem ser obtidas usando o CSS. Neste exemplo, o conteúdo do HtmlMarker consiste em HTML e CSS que criam um pino animado que cai no lugar e nos pulsos.

<br/>

<iframe height='500' scrolling='no' title='Fonte de código HTML' src='//codepen.io/azuremaps/embed/qJVgMx/?height=500&theme-id=0&default-tab=js,result&embed-version=2&editable=true' frameborder='no' allowtransparency='true' allowfullscreen='true' style='width: 100%;'>Consulte a caneta de <a href='https://codepen.io/azuremaps/pen/qJVgMx/'>fonte de código HTML</a> pelo mapas do Azure (<a href='https://codepen.io/azuremaps'>@azuremaps</a>) em <a href='https://codepen.io'>CodePen</a>.
</iframe>

## <a name="draggable-html-markers"></a>Marcadores HTML arrastáveis

Este exemplo mostra como tornar um marcador HTML arrastável. Os marcadores de HTML dão suporte a eventos `drag`, `dragstart`e `dragend`.

<br/>

<iframe height='500' scrolling='no' title='Marcador HTML arrastável' src='//codepen.io/azuremaps/embed/wQZoEV/?height=500&theme-id=0&default-tab=js,result&embed-version=2&editable=true' frameborder='no' allowtransparency='true' allowfullscreen='true' style='width: 100%;'>Consulte o <a href='https://codepen.io/azuremaps/pen/wQZoEV/'>marcador HTML arrastável</a> de caneta pelo Azure Maps (<a href='https://codepen.io/azuremaps'>@azuremaps</a>) em <a href='https://codepen.io'>CodePen</a>.
</iframe>

## <a name="add-mouse-events-to-html-markers"></a>Adicionar eventos de mouse a marcadores HTML

Estes exemplos mostram como adicionar o mouse e arrastar eventos para um marcador HTML.

<br/>

<iframe height='500' scrolling='no' title='Adicionando eventos de mouse a marcadores HTML' src='//codepen.io/azuremaps/embed/RqOKRz/?height=500&theme-id=0&default-tab=js,result&embed-version=2&editable=true' frameborder='no' allowtransparency='true' allowfullscreen='true' style='width: 100%;'>Consulte a caneta <a href='https://codepen.io/azuremaps/pen/RqOKRz/'>adicionando eventos de mouse a marcadores HTML</a> pelo Azure Maps (<a href='https://codepen.io/azuremaps'>@azuremaps</a>) em <a href='https://codepen.io'>CodePen</a>.
</iframe>

## <a name="next-steps"></a>Passos seguintes

Saiba mais sobre as classes e os métodos usados neste artigo:

> [!div class="nextstepaction"]
> [HtmlMarker](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.htmlmarker?view=azure-iot-typescript-latest)

> [!div class="nextstepaction"]
> [HtmlMarkerOptions](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.htmlmarkeroptions?view=azure-iot-typescript-latest)

> [!div class="nextstepaction"]
> [HtmlMarkerManager](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.htmlmarkermanager?view=azure-iot-typescript-latest)

Para obter mais exemplos de código a serem adicionados aos seus mapas, consulte os seguintes artigos:

> [!div class="nextstepaction"]
> [Como usar modelos de imagem](how-to-use-image-templates-web-sdk.md)

> [!div class="nextstepaction"]
> [Adicionar uma camada de símbolo](./map-add-pin.md)

> [!div class="nextstepaction"]
> [Adicionar uma camada de bolha](./map-add-bubble-layer.md)