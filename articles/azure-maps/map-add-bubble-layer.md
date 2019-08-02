---
title: Adicionar uma camada de bolha ao Azure Maps | Microsoft Docs
description: Como adicionar uma camada de bolha ao mapa do JavaScript
author: rbrundritt
ms.author: richbrun
ms.date: 07/29/2019
ms.topic: conceptual
ms.service: azure-maps
services: azure-maps
manager: ''
ms.custom: codepen
ms.openlocfilehash: 516e4f35c88ae9c0e2d63e8a4ee40eb57c05ac29
ms.sourcegitcommit: 3877b77e7daae26a5b367a5097b19934eb136350
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 07/30/2019
ms.locfileid: "68639034"
---
# <a name="add-a-bubble-layer-to-a-map"></a>Adicionar uma camada de bolha a um mapa

Este artigo mostra como você pode renderizar dados de ponto de uma fonte de dados como uma camada de bolha em um mapa. Camadas de bolha renderizam pontos como círculos no mapa com raio de pixel fixo. 

> [!TIP]
> As camadas de bolha por padrão renderizarão as coordenadas de todas as geometrias em uma fonte de dados. Para limitar a camada de modo que ela só processe os recursos de geometria de `filter` ponto, defina a propriedade `['==', ['geometry-type'], 'Point']` da `['any', ['==', ['geometry-type'], 'Point'], ['==', ['geometry-type'], 'MultiPoint']]` camada como ou se você quiser incluir recursos do MultiPoint também.

## <a name="add-a-bubble-layer"></a>Adicionar uma camada de bolha

<iframe height='500' scrolling='no' title='Fonte de BubbleLayer' src='//codepen.io/azuremaps/embed/mzqaKB/?height=500&theme-id=0&default-tab=js,result&embed-version=2&editable=true' frameborder='no' allowtransparency='true' allowfullscreen='true' style='width: 100%;'>Consulte a caneta <a href='https://codepen.io/azuremaps/pen/mzqaKB/'>BubbleLayer DataSource</a> pelo Azure Maps (<a href='https://codepen.io/azuremaps'>@azuremaps</a>) em <a href='https://codepen.io'>CodePen</a>.
</iframe>

No código acima, o primeiro bloco de código constrói um objeto de mapa. Você pode ver [criar um mapa](./map-create.md) para obter instruções.

No segundo bloco de código, uma matriz de objetos [Point](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.data.point?view=azure-iot-typescript-latest) é definida e adicionada ao objeto [DataSource](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.source.datasource?view=azure-iot-typescript-latest) .

Uma [camada de bolha](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.layer.bubblelayer?view=azure-iot-typescript-latest) renderiza os dados baseados em ponto encapsulados na [fonte de dados](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.source.datasource?view=azure-iot-typescript-latest) como círculos no mapa. O último bloco de código cria uma camada de bolha e a adiciona ao mapa. Consulte Propriedades de uma camada de bolha em [BubbleLayerOptions](/javascript/api/azure-maps-control/atlas.bubblelayeroptions).

A matriz de objetos Point, a fonte de dados e a camada de bolha são criadas e adicionadas ao mapa dentro da função de ouvinte de [eventos](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.map?view=azure-iot-typescript-latest#events) para garantir que o círculo seja exibido depois que o mapa tiver sido totalmente carregado.

## <a name="show-labels-with-a-bubble-layer"></a>Mostrar rótulos com uma camada de bolha

<iframe height='500' scrolling='no' title='Fonte de fontes multicamadas' src='//codepen.io/azuremaps/embed/rqbQXy/?height=500&theme-id=0&default-tab=js,result&embed-version=2&editable=true' frameborder='no' allowtransparency='true' allowfullscreen='true' style='width: 100%;'>Consulte a caneta <a href='https://codepen.io/azuremaps/pen/rqbQXy/'>multicamada DataSource</a> por mapas do<a href='https://codepen.io/azuremaps'>@azuremaps</a>Azure () em <a href='https://codepen.io'>CodePen</a>.
</iframe>

O código acima mostra como Visualizar e rotular dados no mapa. O primeiro bloco de código acima constrói um objeto de mapa. Você pode ver [criar um mapa](./map-create.md) para obter instruções.

O segundo bloco de código cria um objeto [Point](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.data.point?view=azure-iot-typescript-latest) . Em seguida, ele cria um objeto de fonte de dados usando a classe de [fonte de dados](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.source.datasource?view=azure-iot-typescript-latest) e adiciona o ponto à fonte de dados.

Uma [camada de bolha](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.layer.bubblelayer?view=azure-iot-typescript-latest) renderiza os dados baseados em ponto encapsulados na [fonte de dados](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.source.datasource?view=azure-iot-typescript-latest) como círculos no mapa. O terceiro bloco de código cria uma camada de bolha e a adiciona ao mapa. Consulte Propriedades de uma camada de bolha em [BubbleLayerOptions](/javascript/api/azure-maps-control/atlas.bubblelayeroptions).

Uma [camada de símbolo](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.layer.symbollayer?view=azure-iot-typescript-latest) usa texto ou ícones para renderizar os dados baseados em ponto encapsulados na [DataSource](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.source.datasource?view=azure-iot-typescript-latest) como símbolos no mapa. O último bloco de código cria e adiciona uma camada de símbolo ao mapa que renderiza o rótulo de texto para a bolha. Consulte Propriedades de uma camada de símbolo em [SymbolLayerOptions](/javascript/api/azure-maps-control/atlas.symbollayeroptions).

A fonte de dados e as camadas são criadas e adicionadas ao mapa na função de ouvinte de [eventos](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.map?view=azure-iot-typescript-latest#events) para garantir que os dados sejam exibidos depois que o mapa tiver sido totalmente carregado.

## <a name="customize-a-bubble-layer"></a>Personalizar uma camada de bolha

A camada de bolha tem apenas algumas opções de estilo. Aqui está uma ferramenta para experimentá-las.

<br/>

<iframe height='700' scrolling='no' title='Opções de camada de bolha' src='//codepen.io/azuremaps/embed/eQxbGm/?height=700&theme-id=0&default-tab=result' frameborder='no' allowtransparency='true' allowfullscreen='true' style='width: 100%;'>Consulte as <a href='https://codepen.io/azuremaps/pen/eQxbGm/'>Opções da camada de bolha</a> da caneta pelo<a href='https://codepen.io/azuremaps'>@azuremaps</a>Azure Maps () em <a href='https://codepen.io'>CodePen</a>.
</iframe>

## <a name="next-steps"></a>Passos Seguintes

Saiba mais sobre as classes e os métodos usados neste artigo:

> [!div class="nextstepaction"]
> [BubbleLayer](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.layer.bubblelayer?view=azure-iot-typescript-latest)

> [!div class="nextstepaction"]
> [BubbleLayerOptions](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.bubblelayeroptions?view=azure-iot-typescript-latest)

Consulte os artigos a seguir para obter mais exemplos de código para adicionar aos seus mapas:

> [!div class="nextstepaction"]
> [Adicionar uma camada de símbolo](map-add-pin.md)

> [!div class="nextstepaction"]
> [Usar expressões de estilo controladas por dados](data-driven-style-expressions-web-sdk.md)