---
title: Adicionar um marcador de HTML para o Azure Maps | Documentos da Microsoft
description: Como adicionar um marcador de HTML para o mapa de Javascript
author: jingjing-z
ms.author: jinzh
ms.date: 05/07/2018
ms.topic: conceptual
ms.service: azure-maps
services: azure-maps
manager: ''
ms.custom: codepen
ms.openlocfilehash: 1c812a77429e13ea39b2f4946043c13e10aaf097
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/23/2019
ms.locfileid: "60769673"
---
# <a name="add-html-markers-to-the-map"></a>Adicionar marcadores HTML ao mapa

Este artigo mostra-lhe como adicionar um HTML personalizado, como um ficheiro de imagem ao mapa como um marcador de HTML.

> [!NOTE]
> Marcadores HTML não ligar a origens de dados. Em vez disso, as informações de posição são adicionadas diretamente para o marcador e marcador é adicionado para os mapas `markers` propriedade que é um [HtmlMarkerManager](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.htmlmarkermanager?view=azure-iot-typescript-latest).

> [!IMPORTANT]
> Ao contrário da maioria das camadas no controle da Web de mapas do Azure que utilizar de WebGL para processamento, marcadores de HTML usar elementos de DOM tradicionais para processamento. Como tal, os marcadores HTML mais adicionado uma página, os elementos DOM mais existem. Depois de adicionar alguns centenas marcadores de HTML pode degradar o desempenho. Para conjuntos de dados maiores, considere o clustering de seus dados ou usando uma camada de símbolo ou de bolhas.

## <a name="add-an-html-marker"></a>Adicionar um marcador HTML

A classe de HtmlMarker tem um estilo predefinido. Pode personalizar o marcador, definindo as opções de cor e o texto do marcador. O estilo do padrão da classe HtmlMarker é um modelo SVG que tenha um marcador de posição de cores e texto. Defina as propriedades de cores e texto nas opções do HtmlMarker para uma personalização rápida. 

<br/>

<iframe height='500' scrolling='no' title='Adicionar um marcador de HTML para um mapa' src='//codepen.io/azuremaps/embed/MVoeVw/?height=500&theme-id=0&default-tab=js,result&embed-version=2&editable=true' frameborder='no' allowtransparency='true' allowfullscreen='true' style='width: 100%;'>Consulte a caneta <a href='https://codepen.io/azuremaps/pen/MVoeVw/'>adicionar um marcador de HTML para um mapa</a> ao Azure Maps (<a href='https://codepen.io/azuremaps'>@azuremaps</a>) na <a href='https://codepen.io'>CodePen</a>.
</iframe>

No código acima, o primeiro bloco de código constrói um objeto de mapa. Pode ver [criar um mapa](./map-create.md) para obter instruções.

O segundo bloco de código adiciona uma [HtmlMarker](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.htmlmarker?view=azure-iot-typescript-latest) para o mapa, com o [marcadores](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.map?view=azure-iot-typescript-latest#markers) propriedade do [mapa](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.map?view=azure-iot-typescript-latest) classe. O HtmlMarker é adicionado ao mapa dentro do [serviço de escuta de eventos](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.map?view=azure-iot-typescript-latest#events) função para garantir que é apresentado depois do mapa carrega totalmente.

## <a name="create-svg-templated-html-marker"></a>Criar marcador HTML com modelos SVG

A predefinição `htmlContent` de um Html marcador é um modelo SVG com pastas de lugar `{color}` e `{text}` nela. Pode criar cadeias de caracteres SVG personalizadas e adicionar estes marcadores de posição mesmo em seu SVG, de modo a que definir o `color` e `text` opções do marcador atualizar estes marcadores de posição no seu SVG.

<br/>

<iframe height='500' scrolling='no' title='Marcador de HTML com o modelo personalizado SVG' src='//codepen.io/azuremaps/embed/LXqMWx/?height=500&theme-id=0&default-tab=js,result&embed-version=2&editable=true' frameborder='no' allowtransparency='true' allowfullscreen='true' style='width: 100%;'>Consulte a caneta <a href='https://codepen.io/azuremaps/pen/LXqMWx/'>HTML marcador com modelo personalizado SVG</a> através do Azure Maps (<a href='https://codepen.io/azuremaps'>@azuremaps</a>) na <a href='https://codepen.io'>CodePen</a>.
</iframe>

## <a name="add-a-css-styled-html-marker"></a>Adicionar que um CSS com estilo marcador HTML

Uma das vantagens dos marcadores HTML é que existem muitas personalizações excelentes que podem ser alcançadas usando CSS. Neste exemplo, o conteúdo do HtmlMarker consiste em HTML e CSS que crie um pin animado que solta no local e pulsos.

<br/>

<iframe height='500' scrolling='no' title='Origem de dados do HTML' src='//codepen.io/azuremaps/embed/qJVgMx/?height=500&theme-id=0&default-tab=js,result&embed-version=2&editable=true' frameborder='no' allowtransparency='true' allowfullscreen='true' style='width: 100%;'>Consulte a caneta <a href='https://codepen.io/azuremaps/pen/qJVgMx/'>origem de dados do HTML</a> através do Azure Maps (<a href='https://codepen.io/azuremaps'>@azuremaps</a>) na <a href='https://codepen.io'>CodePen</a>.
</iframe>

## <a name="draggable-html-markers"></a>Arrastável marcadores HTML

Este exemplo mostra como tornar um marcador HTML arrastável. Suportam de marcadores HTML `drag`, `dragstart` e `dragend` eventos.

<br/>

<iframe height='500' scrolling='no' title='Arrastável HTML marcador' src='//codepen.io/azuremaps/embed/wQZoEV/?height=500&theme-id=0&default-tab=js,result&embed-version=2&editable=true' frameborder='no' allowtransparency='true' allowfullscreen='true' style='width: 100%;'>Consulte a caneta <a href='https://codepen.io/azuremaps/pen/wQZoEV/'>arrastável HTML marcador</a> através do Azure Maps (<a href='https://codepen.io/azuremaps'>@azuremaps</a>) na <a href='https://codepen.io'>CodePen</a>.
</iframe>

## <a name="add-mouse-events-to-html-markers"></a>Adicionar eventos de mouse para os marcadores HTML

Estes exemplos mostram como adicionar o mouse e arraste os eventos para um marcador HTML.

<br/>

<iframe height='500' scrolling='no' title='Adição de eventos de Mouse marcadores HTML' src='//codepen.io/azuremaps/embed/RqOKRz/?height=500&theme-id=0&default-tab=js,result&embed-version=2&editable=true' frameborder='no' allowtransparency='true' allowfullscreen='true' style='width: 100%;'>Consulte a caneta <a href='https://codepen.io/azuremaps/pen/RqOKRz/'>adição de eventos de Mouse HTML marcadores</a> através do Azure Maps (<a href='https://codepen.io/azuremaps'>@azuremaps</a>) na <a href='https://codepen.io'>CodePen</a>.
</iframe>

## <a name="next-steps"></a>Passos Seguintes

Saiba mais sobre as classes e métodos usados neste artigo:

> [!div class="nextstepaction"]
> [HtmlMarker](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.htmlmarker?view=azure-iot-typescript-latest)

> [!div class="nextstepaction"]
> [HtmlMarkerOptions](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.htmlmarkeroptions?view=azure-iot-typescript-latest)

> [!div class="nextstepaction"]
> [HtmlMarkerManager](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.htmlmarkermanager?view=azure-iot-typescript-latest)

Para obter mais exemplos de código adicionar a seus mapas, veja os artigos seguintes:

> [!div class="nextstepaction"]
> [Adicionar uma camada de símbolo](./map-add-pin.md)

> [!div class="nextstepaction"]
> [Adicionar uma camada de bolhas](./map-add-bubble-layer.md)