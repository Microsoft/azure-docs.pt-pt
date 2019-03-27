---
title: Adicionar uma forma com o Azure Maps | Documentos da Microsoft
description: Como adicionar uma forma para um mapa de Javascript
author: jingjing-z
ms.author: jinzh
ms.date: 10/30/2018
ms.topic: conceptual
ms.service: azure-maps
services: azure-maps
manager: ''
ms.custom: codepen
ms.openlocfilehash: 7598bbc879351752580247e46bc986ee84fa0d56
ms.sourcegitcommit: f24fdd1ab23927c73595c960d8a26a74e1d12f5d
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/27/2019
ms.locfileid: "58497249"
---
# <a name="add-a-shape-to-a-map"></a>Adicionar uma forma para um mapa

Este artigo mostra-lhe como adicionar um [forma](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.shape?view=azure-iot-typescript-latest) para as propriedades de mapa e atualização de uma forma existente no mapa.

<a id="addALine"></a>

## <a name="add-a-line"></a>Adicionar uma linha

<iframe height='500' scrolling='no' title='Adicionar uma linha para um mapa' src='//codepen.io/azuremaps/embed/qomaKv/?height=500&theme-id=0&default-tab=js,result&embed-version=2&editable=true' frameborder='no' allowtransparency='true' allowfullscreen='true' style='width: 100%;'>Consulte a caneta <a href='https://codepen.io/azuremaps/pen/qomaKv/'>adicionar uma linha para um mapa</a> ao Azure Maps (<a href='https://codepen.io/azuremaps'>@azuremaps</a>) na <a href='https://codepen.io'>CodePen</a>.
</iframe>

O primeiro bloco de código no código acima constrói um objeto de mapa. Pode ver [criar um mapa](./map-create.md) para obter instruções.

O segundo bloco de código, um objeto de origem de dados é criado utilizando o [origem de dados](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.source.datasource?view=azure-iot-typescript-latest) classe. R [LineString](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.data.linestring?view=azure-iot-typescript-latest) objeto é criado e adicionado à origem de dados.

R [LineLayer](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.layer.linelayer?view=azure-iot-typescript-latest) composições linha objetos encapsulados no [DataSource](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.source.datasource?view=azure-iot-typescript-latest). O último bloco de código cria e adiciona uma camada de linhas ao mapa. Ver as propriedades de uma camada de linha em [LineLayerOptions](/javascript/api/azure-maps-control/atlas.linelayeroptions?view=azure-iot-typescript-latest). A origem de dados e a camada de linhas são criadas e adicionadas ao mapa dentro do [serviço de escuta de eventos](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.map?view=azure-iot-typescript-latest#events) função para garantir que a linha é apresentada depois que o mapa for totalmente carregada.

## <a name="customize-a-line-layer"></a>Personalizar uma camada de linha

A linha camada várias opções de estilo. Aqui está uma ferramenta de experimentá-las.

<br/>

<iframe height='700' scrolling='no' title='Opções da linha de camada' src='//codepen.io/azuremaps/embed/GwLrgb/?height=700&theme-id=0&default-tab=result' frameborder='no' allowtransparency='true' allowfullscreen='true' style='width: 100%;'>Consulte a caneta <a href='https://codepen.io/azuremaps/pen/GwLrgb/'>camada opções de linha</a> através do Azure Maps (<a href='https://codepen.io/azuremaps'>@azuremaps</a>) na <a href='https://codepen.io'>CodePen</a>.
</iframe>

<a id="addACircle"></a>

## <a name="add-a-circle"></a>Adicione um círculo

<iframe height='500' scrolling='no' title='Adicione um círculo para um mapa' src='//codepen.io/azuremaps/embed/PRmzJX/?height=500&theme-id=0&default-tab=js,result&embed-version=2&editable=true' frameborder='no' allowtransparency='true' allowfullscreen='true' style='width: 100%;'>Consulte a caneta <a href='https://codepen.io/azuremaps/pen/PRmzJX/'>adicione um círculo para um mapa</a> ao Azure Maps (<a href='https://codepen.io/azuremaps'>@azuremaps</a>) na <a href='https://codepen.io'>CodePen</a>.
</iframe>

O primeiro bloco de código no código acima constrói um objeto de mapa. Pode ver [criar um mapa](./map-create.md) para obter instruções.

O segundo bloco de código, um objeto de origem de dados é criado utilizando o [origem de dados](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.source.datasource?view=azure-iot-typescript-latest) classe. Um círculo é um [funcionalidade](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.data.feature?view=azure-iot-typescript-latest) dos [ponto](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.data.point?view=azure-iot-typescript-latest) e tem um `subType` propriedade definida como "círculo" e um `radius` medidores de valor de propriedade. Quando um recurso de ponto com um subtipo do círculo é adicionado a uma origem de dados, ele converte-o num polígono circular no mapa.

R [PolygonLayer](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.layer.polygonlayer?view=azure-iot-typescript-latest) processa os dados encapsulados no [DataSource](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.source.datasource?view=azure-iot-typescript-latest) no mapa. O último bloco de código cria e adiciona uma camada de polígonos ao mapa. Ver as propriedades de uma camada de polígonos no [PolygonLayerOptions](/javascript/api/azure-maps-control/atlas.polygonlayeroptions?view=azure-iot-typescript-latest). A origem de dados e a camada de polígonos são criadas e adicionadas ao mapa dentro do [serviço de escuta de eventos](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.map?view=azure-iot-typescript-latest#events) função para garantir que o círculo é apresentado depois que o mapa for totalmente carregada.

<a id="addAPolygon"></a>

## <a name="add-a-polygon"></a>Adicionar um polígono

Existem duas formas diferentes, pode adicionar um polígono ao mapa. Ambos são explicadas nos exemplos a seguir.

### <a name="use-polygon-layer"></a>Utilizar a camada de polígonos 

<iframe height='500' scrolling='no' title='Adicionar um polígono para um mapa ' src='//codepen.io/azuremaps/embed/yKbOvZ/?height=500&theme-id=0&default-tab=js,result&embed-version=2&editable=true' frameborder='no' allowtransparency='true' allowfullscreen='true' style='width: 100%;'>Consulte a caneta <a href='https://codepen.io/azuremaps/pen/yKbOvZ/'>adicionar um polígono para um mapa </a> ao Azure Maps (<a href='https://codepen.io/azuremaps'>@azuremaps</a>) na <a href='https://codepen.io'>CodePen</a>.
</iframe>

No código acima, o primeiro bloco de código constrói um objeto de mapa. Pode ver [criar um mapa](./map-create.md) para obter instruções.

O segundo bloco de código, um objeto de origem de dados é criado utilizando o [origem de dados](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.source.datasource?view=azure-iot-typescript-latest) classe. R [polígono](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.data.polygon?view=azure-iot-typescript-latest) é criada a partir de uma matriz de coordenadas e adicionado à origem de dados. 

R [PolygonLayer](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.layer.polygonlayer?view=azure-iot-typescript-latest) processa os dados encapsulados no [DataSource](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.source.datasource?view=azure-iot-typescript-latest) no mapa. O último bloco de código cria e adiciona uma camada de polígonos ao mapa. Ver as propriedades de uma camada de polígonos no [PolygonLayerOptions](/javascript/api/azure-maps-control/atlas.polygonlayeroptions?view=azure-iot-typescript-latest). A origem de dados e a camada de polígonos são criadas e adicionadas ao mapa dentro do [serviço de escuta de eventos](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.map?view=azure-iot-typescript-latest#events) função para garantir que o polígono é apresentado depois que o mapa for totalmente carregada.

### <a name="use-polygon-and-line-layer"></a>Utilizar a camada de polígonos e de linhas

<iframe height='500' scrolling='no' title='Camada de polígonos e de linhas para adicionar o polígono' src='//codepen.io/azuremaps/embed/aRyEPy/?height=500&theme-id=0&default-tab=js,result&embed-version=2&editable=true' frameborder='no' allowtransparency='true' allowfullscreen='true' style='width: 100%;'>Consulte a caneta <a href='https://codepen.io/azuremaps/pen/aRyEPy/'>camada de polígonos e de linhas para adicionar o polígono</a> ao Azure Maps (<a href='https://codepen.io/azuremaps'>@azuremaps</a>) na <a href='https://codepen.io'>CodePen</a>.
</iframe>

No código acima, o primeiro bloco de código constrói um objeto de mapa. Pode ver [criar um mapa](./map-create.md) para obter instruções.

O segundo bloco de código, um objeto de origem de dados é criado utilizando o [origem de dados](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.source.datasource?view=azure-iot-typescript-latest) classe. R [polígono](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.data.polygon?view=azure-iot-typescript-latest) é criada a partir de uma matriz de coordenadas e adicionado à origem de dados. 

R [PolygonLayer](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.layer.polygonlayer?view=azure-iot-typescript-latest) processa os dados encapsulados no [DataSource](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.source.datasource?view=azure-iot-typescript-latest) no mapa. Ver as propriedades de uma camada de polígonos no [PolygonLayerOptions](/javascript/api/azure-maps-control/atlas.polygonlayeroptions?view=azure-iot-typescript-latest). R [LineLayer](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.layer.linelayer?view=azure-iot-typescript-latest) é uma matriz de linhas. Ver as propriedades de uma camada de linha em [LineLayerOptions](/javascript/api/azure-maps-control/atlas.linelayeroptions?view=azure-iot-typescript-latest). O terceiro bloco de código cria as camadas de polígonos e de linhas.

O último bloco de código adiciona as camadas de polígonos e de linhas ao mapa. A origem de dados e as camadas são criadas e adicionadas ao mapa dentro do [serviço de escuta de eventos](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.map?view=azure-iot-typescript-latest#events) função para garantir que o polígono é apresentado depois que o mapa for totalmente carregada.

## <a name="customize-a-polygon-layer"></a>Personalizar uma camada de polígonos

A camada de polígonos tem apenas algumas opções de estilo. Aqui está uma ferramenta de experimentá-las.

<br/>

<iframe height='700' scrolling='no' title='LXvxpg' src='//codepen.io/azuremaps/embed/LXvxpg/?height=700&theme-id=0&default-tab=result' frameborder='no' allowtransparency='true' allowfullscreen='true' style='width: 100%;'>Consulte a caneta <a href='https://codepen.io/azuremaps/pen/LXvxpg/'>LXvxpg</a> através do Azure Maps (<a href='https://codepen.io/azuremaps'>@azuremaps</a>) na <a href='https://codepen.io'>CodePen</a>.
</iframe>

## <a name="update-a-shape"></a>Uma forma de atualização

Uma classe Shape envolve uma [Geometry](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.data.geometry?view=azure-iot-typescript-latest) ou [funcionalidade](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.data.feature?view=azure-iot-typescript-latest) e torna mais fácil de atualizar e mantê-los.
`new Shape(data: Feature<data.Geometry, any>)` constrói um objeto de forma e o inicializa com o recurso especificado.

<br/>

<iframe height='500' scrolling='no' title='Atualizar propriedades de forma' src='//codepen.io/azuremaps/embed/ZqMeQY/?height=500&theme-id=0&default-tab=js,result&embed-version=2&editable=true' frameborder='no' allowtransparency='true' allowfullscreen='true' style='width: 100%;'>Consulte a caneta <a href='https://codepen.io/azuremaps/pen/ZqMeQY/'>atualizar propriedades de forma</a> ao Azure Maps (<a href='https://codepen.io/azuremaps'>@azuremaps</a>) na <a href='https://codepen.io'>CodePen</a>.
</iframe>

O primeiro bloco de código acima constrói um objeto de mapa. Pode ver [criar um mapa](./map-create.md) para obter instruções.

É um ponto de um [funcionalidade](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.data.feature?view=azure-iot-typescript-latest) dos [ponto](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.data.point?view=azure-iot-typescript-latest) a classe. O segundo bloco de código inicializa o valor de radius para o elemento do controlo de deslize HTML e, em seguida, constrói e encapsula um objeto de ponto numa [forma](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.shape?view=azure-iot-typescript-latest) objeto da classe.

O terceiro bloco de código cria uma função que usa o valor do elemento de controlo de deslize de intervalo de HTML e altera o valor de radius usando a classe shape [addProperty](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.shape?view=azure-iot-typescript-latest) método.

No quarto bloco de código, é criado um objeto de origem de dados usando o [origem de dados](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.source.datasource?view=azure-iot-typescript-latest) classe. O ponto de, em seguida, é adicionado à origem de dados.

R [PolygonLayer](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.layer.polygonlayer?view=azure-iot-typescript-latest) processa os dados encapsulados no [DataSource](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.source.datasource?view=azure-iot-typescript-latest) no mapa. O terceiro bloco de código cria uma camada de polígonos. Ver as propriedades de uma camada de polígonos no [PolygonLayerOptions](/javascript/api/azure-maps-control/atlas.polygonlayeroptions?view=azure-iot-typescript-latest). A origem de dados, o serviço de escuta de eventos de clique e a camada de polígonos são criadas e adicionadas ao mapa dentro do [serviço de escuta de eventos](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.map?view=azure-iot-typescript-latest#events) função para garantir que o ponto é apresentado depois que o mapa for totalmente carregada.

## <a name="next-steps"></a>Passos Seguintes

Para obter mais exemplos de código adicionar a seus mapas, veja os artigos seguintes:

> [!div class="nextstepaction"]
> [Marcadores HTML](./map-add-custom-html.md)

> [!div class="nextstepaction"]
> [Adicionar uma camada de mapa térmico](./map-add-heat-map-layer.md)
