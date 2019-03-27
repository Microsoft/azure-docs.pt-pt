---
title: Adicionar uma camada de símbolo para o Azure Maps | Documentos da Microsoft
description: Como adicionar os símbolos para o mapa de Javascript
author: rbrundritt
ms.author: richbrun
ms.date: 12/2/2018
ms.topic: conceptual
ms.service: azure-maps
services: azure-maps
manager: ''
ms.custom: codepen
ms.openlocfilehash: a4d1a54e94b3228c64352bf08cd8cc69820a5e2d
ms.sourcegitcommit: f24fdd1ab23927c73595c960d8a26a74e1d12f5d
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/27/2019
ms.locfileid: "58500054"
---
# <a name="add-a-symbol-layer-to-a-map"></a>Adicionar uma camada de símbolo para um mapa

Este artigo mostra como pode processar dados de ponto de uma origem de dados como uma camada de símbolo num mapa. Camadas de símbolo são processadas através de WebGL e suportam pontos de dados significativamente mais do que marcadores HTML, mas não oferecem suporte a tradicionais elementos HTML e CSS para definição de estilo.  

> [!TIP]
> Camadas de símbolo por predefinição processará as coordenadas de todas as geometrias numa origem de dados. Para limitar a camada de forma que ele só processa geometria de ponto de conjunto de funcionalidades a `filter` propriedade da camada para `['==', '$type', 'Point']`

## <a name="add-a-symbol-layer"></a>Adicionar uma camada de símbolo

<iframe height='500' scrolling='no' title='Fixar localização de comutador' src='//codepen.io/azuremaps/embed/ZqJjRP/?height=500&theme-id=0&default-tab=js,result&embed-version=2&editable=true' frameborder='no' allowtransparency='true' allowfullscreen='true' style='width: 100%;'>Consulte a caneta <a href='https://codepen.io/azuremaps/pen/ZqJjRP/'>fixar localização de comutador</a> ao Azure Maps (<a href='https://codepen.io/azuremaps'>@azuremaps</a>) na <a href='https://codepen.io'>CodePen</a>.
</iframe>

O primeiro bloco de código acima constrói um objeto de mapa. Pode ver [criar um mapa](./map-create.md) para obter instruções.

O segundo bloco de código, um objeto de origem de dados é criado utilizando o [origem de dados](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.source.datasource?view=azure-iot-typescript-latest) classe. Um [recursos] que contém um [ponto](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.data.point?view=azure-iot-typescript-latest) geometry é encapsulada pela [forma](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.shape?view=azure-iot-typescript-latest) de classe para facilitar atualizar, em seguida, criado e adicionado à origem de dados.

O terceiro bloco de código cria um [serviço de escuta de eventos](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.map?view=azure-iot-typescript-latest#events) e atualizações coordenadas do ponto de após rato clique usando a classe shape [setCoordinates](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.shape?view=azure-iot-typescript-latest) método.

Uma [camada de símbolo](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.layer.symbollayer?view=azure-iot-typescript-latest) usa ou ícones de texto para processar dados com base no ponto encapsulados no [DataSource](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.source.datasource?view=azure-iot-typescript-latest) como símbolos no mapa.  A origem de dados, o serviço de escuta de eventos de clique e a camada de símbolo são criadas e adicionadas ao mapa dentro do [serviço de escuta de eventos](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.map?view=azure-iot-typescript-latest#events) função para garantir que o ponto é apresentado depois que o mapa for totalmente carregada.

> [!TIP]
> Por predefinição, para desempenho, camadas de símbolo otimizam o processamento de símbolos, ocultando símbolos que se sobrepõem. Como ampliar os símbolos ocultos se tornar visível. Para desativar esta funcionalidade e processar todos os símbolos durante todo o tempo, defina o `allowOverlap` propriedade o `iconOptions` opções para `true`.

## <a name="add-a-custom-icon-to-a-symbol-layer"></a>Adicione um ícone personalizado para uma camada de símbolo

Camadas de símbolo são renderizadas através de WebGL. Como tais todos os recursos, como imagens de ícone, têm de ser carregados para o contexto de WebGL. Este exemplo mostra como adicionar um ícone de símbolo personalizados para os recursos de mapa e, em seguida, utilizá-lo para renderizar o ponto de dados com um símbolo personalizado no mapa. O `textField` propriedade da camada de símbolo exige uma expressão de ser especificado. Neste caso, queremos processar a propriedade de temperatura da funcionalidade de ponto a como o valor de texto. Isto pode ser obtido com esta expressão: `['get', 'temperature']`. 

<br/>

<iframe height='500' scrolling='no' title='Ícone de imagem do símbolo personalizado' src='//codepen.io/azuremaps/embed/WYWRWZ/?height=500&theme-id=0&default-tab=js,result&embed-version=2&editable=true' frameborder='no' allowtransparency='true' allowfullscreen='true' style='width: 100%;'>Consulte a caneta <a href='https://codepen.io/azuremaps/pen/WYWRWZ/'>ícone de imagem personalizada do símbolo</a> através do Azure Maps (<a href='https://codepen.io/azuremaps'>@azuremaps</a>) na <a href='https://codepen.io'>CodePen</a>.
</iframe>

## <a name="customize-a-symbol-layer"></a>Personalizar uma camada de símbolo 

A camada de símbolo tem muitas opções de estilo disponíveis. Aqui está uma ferramenta para testar essas várias opções de estilo.

<br/>

<iframe height='700' scrolling='no' title='Opções de camada de símbolo' src='//codepen.io/azuremaps/embed/PxVXje/?height=700&theme-id=0&default-tab=result' frameborder='no' allowtransparency='true' allowfullscreen='true' style='width: 100%;'>Consulte a caneta <a href='https://codepen.io/azuremaps/pen/PxVXje/'>opções de camada do símbolo</a> através do Azure Maps (<a href='https://codepen.io/azuremaps'>@azuremaps</a>) na <a href='https://codepen.io'>CodePen</a>.
</iframe>

## <a name="next-steps"></a>Passos Seguintes

Saiba mais sobre as classes e métodos usados neste artigo:

> [!div class="nextstepaction"]
> [SymbolLayer](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.layer.symbollayer?view=azure-iot-typescript-latest)

> [!div class="nextstepaction"]
> [SymbolLayerOptions](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.symbollayeroptions?view=azure-iot-typescript-latest)

> [!div class="nextstepaction"]
> [IconOptions](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.iconoptions?view=azure-iot-typescript-latest)

> [!div class="nextstepaction"]
> [Textoopções](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.textoptions?view=azure-iot-typescript-latest)

Veja os artigos seguintes para obter mais amostras de código adicionar a seus mapas:

> [!div class="nextstepaction"]
> [Adicionar um pop-up](./map-add-popup.md)

> [!div class="nextstepaction"]
> [Adicionar uma forma](./map-add-shape.md)

> [!div class="nextstepaction"]
> [Adicionar uma camada de bolhas](./map-add-bubble-layer.md)

> [!div class="nextstepaction"]
> [Adicionar os criadores de HTML](./map-add-bubble-layer.md)
