---
title: Adicionar uma camada de bolha para o Azure Maps | Documentos da Microsoft
description: Como adicionar uma camada de bolha para o mapa de Javascript
author: rbrundritt
ms.author: richbrun
ms.date: 10/30/2018
ms.topic: conceptual
ms.service: azure-maps
services: azure-maps
manager: ''
ms.custom: codepen
ms.openlocfilehash: f2c4c6b8655d5efb993a2dedf536000ac94328c2
ms.sourcegitcommit: c174d408a5522b58160e17a87d2b6ef4482a6694
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/18/2019
ms.locfileid: "59281493"
---
# <a name="add-a-bubble-layer-to-a-map"></a>Adicionar uma camada de bolha para um mapa

Este artigo mostra como pode processar dados de ponto de uma origem de dados como uma camada de bolha num mapa. Camadas de bolhas compostos pontos como círculos no mapa com o radius de pixel fixo. 

> [!TIP]
> Camadas de bolhas, por predefinição processará as coordenadas de todas as geometrias numa origem de dados. Para limitar a camada de forma que ele só processa geometria de ponto de conjunto de funcionalidades do `filter` propriedade da camada para `['==', ['geometry-type'], 'Point']` ou `['any', ['==', ['geometry-type'], 'Point'], ['==', ['geometry-type'], 'MultiPoint']]` se quiser incluem também funcionalidades MultiPoint.

## <a name="add-a-bubble-layer"></a>Adicionar uma camada de bolha

<iframe height='500' scrolling='no' title='Origem de dados de BubbleLayer' src='//codepen.io/azuremaps/embed/mzqaKB/?height=500&theme-id=0&default-tab=js,result&embed-version=2&editable=true' frameborder='no' allowtransparency='true' allowfullscreen='true' style='width: 100%;'>Consulte a caneta <a href='https://codepen.io/azuremaps/pen/mzqaKB/'>BubbleLayer DataSource</a> através do Azure Maps (<a href='https://codepen.io/azuremaps'>@azuremaps</a>) na <a href='https://codepen.io'>CodePen</a>.
</iframe>

No código acima, o primeiro bloco de código constrói um objeto de mapa. Pode ver [criar um mapa](./map-create.md) para obter instruções.

No segundo bloco de código, uma matriz de [ponto](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.data.point?view=azure-iot-typescript-latest) objetos é definidos e adicionados à [DataSource](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.source.datasource?view=azure-iot-typescript-latest) objeto.

Uma [camada de bolhas](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.layer.bubblelayer?view=azure-iot-typescript-latest) processa os dados com base no ponto encapsulados no [origem de dados](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.source.datasource?view=azure-iot-typescript-latest) como faz um movimento no mapa. O último bloco de código cria uma camada de bolhas e adiciona-o ao mapa. Ver as propriedades de uma camada de bolhas na [BubbleLayerOptions](/javascript/api/azure-maps-control/atlas.bubblelayeroptions).

A matriz de objetos Point, a origem de dados e a camada de bolhas são criadas e adicionadas ao mapa dentro de [serviço de escuta de eventos](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.map?view=azure-iot-typescript-latest#events) função para garantir que o círculo é apresentado depois do mapa estiver completamente carregado.

## <a name="show-labels-with-a-bubble-layer"></a>Mostrar etiquetas com uma camada de bolhas

<iframe height='500' scrolling='no' title='Origem de dados multiLayer' src='//codepen.io/azuremaps/embed/rqbQXy/?height=500&theme-id=0&default-tab=js,result&embed-version=2&editable=true' frameborder='no' allowtransparency='true' allowfullscreen='true' style='width: 100%;'>Consulte a caneta <a href='https://codepen.io/azuremaps/pen/rqbQXy/'>MultiLayer DataSource</a> através do Azure Maps (<a href='https://codepen.io/azuremaps'>@azuremaps</a>) na <a href='https://codepen.io'>CodePen</a>.
</iframe>

O código acima mostra-lhe como visualizar e dados da etiqueta do mapa. O primeiro bloco de código acima constrói um objeto de mapa. Pode ver [criar um mapa](./map-create.md) para obter instruções.

O segundo bloco de código, cria um [ponto](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.data.point?view=azure-iot-typescript-latest) objeto. Em seguida, cria uma origem de dados objeto utilizando a [origem de dados](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.source.datasource?view=azure-iot-typescript-latest) de classe e adiciona o ponto à origem de dados.

Uma [camada de bolhas](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.layer.bubblelayer?view=azure-iot-typescript-latest) processa os dados com base no ponto encapsulados no [origem de dados](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.source.datasource?view=azure-iot-typescript-latest) como faz um movimento no mapa. O terceiro bloco de código cria uma camada de bolhas e adiciona-o ao mapa. Ver as propriedades de uma camada de bolhas na [BubbleLayerOptions](/javascript/api/azure-maps-control/atlas.bubblelayeroptions).

Uma [camada de símbolo](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.layer.symbollayer?view=azure-iot-typescript-latest) usa ou ícones de texto para processar dados com base no ponto encapsulados no [DataSource](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.source.datasource?view=azure-iot-typescript-latest) como símbolos no mapa. O último bloco de código cria e adiciona uma camada de símbolo ao mapa que processa o rótulo de texto para a bolha. Ver as propriedades de uma camada de símbolo em [SymbolLayerOptions](/javascript/api/azure-maps-control/atlas.symbollayeroptions).

A origem de dados e as camadas são criadas e adicionadas ao mapa dentro de [serviço de escuta de eventos](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.map?view=azure-iot-typescript-latest#events) função para garantir que os dados são apresentados depois do mapa estiver completamente carregado.

## <a name="customize-a-bubble-layer"></a>Personalizar uma camada de bolhas

A camada de bolhas tem apenas algumas opções de estilo. Aqui está uma ferramenta de experimentá-las.

<br/>

<iframe height='700' scrolling='no' title='Opções de camada de bolhas' src='//codepen.io/azuremaps/embed/eQxbGm/?height=700&theme-id=0&default-tab=result' frameborder='no' allowtransparency='true' allowfullscreen='true' style='width: 100%;'>Consulte a caneta <a href='https://codepen.io/azuremaps/pen/eQxbGm/'>propagar as opções da camada</a> através do Azure Maps (<a href='https://codepen.io/azuremaps'>@azuremaps</a>) na <a href='https://codepen.io'>CodePen</a>.
</iframe>

## <a name="next-steps"></a>Passos Seguintes

Saiba mais sobre as classes e métodos usados neste artigo:

> [!div class="nextstepaction"]
> [BubbleLayer](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.layer.bubblelayer?view=azure-iot-typescript-latest)

> [!div class="nextstepaction"]
> [BubbleLayerOptions](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.bubblelayeroptions?view=azure-iot-typescript-latest)

Veja os artigos seguintes para obter mais amostras de código adicionar a seus mapas:

> [!div class="nextstepaction"]
> [Adicionar uma camada de símbolo](map-add-pin.md)

> [!div class="nextstepaction"]
> [Utilizar expressões de estilo condicionada por dados](data-driven-style-expressions-web-sdk.md)