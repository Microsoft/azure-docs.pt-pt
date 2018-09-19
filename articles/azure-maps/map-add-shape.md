---
title: Adicionar uma forma com o Azure Maps | Documentos da Microsoft
description: Como adicionar uma forma para um mapa de Javascript
author: jingjing-z
ms.author: jinzh
ms.date: 05/07/2018
ms.topic: conceptual
ms.service: azure-maps
services: azure-maps
manager: ''
ms.custom: codepen
ms.openlocfilehash: 6b78c7f5d7d1b5ad4db9401bf2138502c9bbd397
ms.sourcegitcommit: f10653b10c2ad745f446b54a31664b7d9f9253fe
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 09/18/2018
ms.locfileid: "46121916"
---
# <a name="add-a-shape-to-a-map"></a>Adicionar uma forma para um mapa

Este artigo mostra-lhe como adicionar uma linha, um círculo e um polígono ao mapa. 

<a id="addALine"></a>

## <a name="add-a-line"></a>Adicionar uma linha

<iframe height='500' scrolling='no' title='Adicionar uma linha para um mapa' src='//codepen.io/azuremaps/embed/qomaKv/?height=534&theme-id=0&default-tab=js,result&embed-version=2&editable=true' frameborder='no' allowtransparency='true' allowfullscreen='true' style='width: 100%;'>Consulte a caneta <a href='https://codepen.io/azuremaps/pen/qomaKv/'>adicionar uma linha para um mapa</a> ao Azure Maps (<a href='https://codepen.io/azuremaps'>@azuremaps</a>) na <a href='https://codepen.io'>CodePen</a>.
</iframe>

No código acima, o primeiro bloco de código constrói um objeto de mapa. Pode ver [criar um mapa](./map-create.md) para obter instruções.

O segundo bloco de código, é criada uma linha. Uma linha é uma [funcionalidade](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.data.feature?view=azure-iot-typescript-latest) de LineString com LineStringProperties como sua propriedade de recurso. Utilize `new atlas.data.Feature(new atlas.data.LineString())` para criar uma linha e definir as respetivas propriedades.

Uma camada de linha é uma matriz de linhas. O último bloco de código utiliza [addLineStrings](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.map?view=azure-iot-typescript-latest#addlinestrings) função da classe map para adicionar a camada de linhas ao mapa e definir as propriedades da camada de linha. Ver as propriedades de uma camada de linha em [LinestringLayerOptions](https://docs.microsoft.com/javascript/api/azure-maps-control/models.linestringlayeroptions?view=azure-iot-typescript-latest).

<a id="addACircle"></a>

## <a name="add-a-circle"></a>Adicione um círculo

<iframe height='500' scrolling='no' title='Adicione um círculo para um mapa' src='//codepen.io/azuremaps/embed/PRmzJX/?height=538&theme-id=0&default-tab=js,result&embed-version=2&editable=true' frameborder='no' allowtransparency='true' allowfullscreen='true' style='width: 100%;'>Consulte a caneta <a href='https://codepen.io/azuremaps/pen/PRmzJX/'>adicione um círculo para um mapa</a> ao Azure Maps (<a href='https://codepen.io/azuremaps'>@azuremaps</a>) na <a href='https://codepen.io'>CodePen</a>.
</iframe>

No código acima, o primeiro bloco de código constrói um objeto de mapa. Pode ver [criar um mapa](./map-create.md) para obter instruções.

O segundo bloco de código, é criado um círculo. Um círculo é um [funcionalidade](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.data.feature?view=azure-iot-typescript-latest) dos [ponto](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.data.point?view=azure-iot-typescript-latest) com [CircleProperties](https://docs.microsoft.com/javascript/api/azure-maps-control/models.circleproperties?view=azure-iot-typescript-latest) como sua propriedade de recurso. Utilize `new atlas.data.Feature(new atlas.data.Point())` para criar um círculo e definir as respetivas propriedades.

Uma camada do círculo é uma matriz de círculos. O último bloco de código utiliza [addCircle](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.map?view=azure-iot-typescript-latest#addcircles) função da classe map para adicionar a camada do círculo ao mapa e definir as propriedades da camada do círculo. Ver as propriedades de uma camada de círculo em [CircleLayerOptions](https://docs.microsoft.com/javascript/api/azure-maps-control/models.circlelayeroptions?view=azure-iot-typescript-latest).

<a id="addAPolygon"></a>

## <a name="add-a-polygon"></a>Adicionar um polígono

<iframe height='500' scrolling='no' title='Adicionar um polígono para um mapa ' src='//codepen.io/azuremaps/embed/yKbOvZ/?height=543&theme-id=0&default-tab=js,result&embed-version=2&editable=true' frameborder='no' allowtransparency='true' allowfullscreen='true' style='width: 100%;'>Consulte a caneta <a href='https://codepen.io/azuremaps/pen/yKbOvZ/'>adicionar um polígono para um mapa </a> ao Azure Maps (<a href='https://codepen.io/azuremaps'>@azuremaps</a>) na <a href='https://codepen.io'>CodePen</a>.
</iframe>

No código acima, o primeiro bloco de código constrói um objeto de mapa. Pode ver [criar um mapa](./map-create.md) para obter instruções.

O segundo bloco de código, é criado um polígono. Um polígono é um [funcionalidade](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.data.feature?view=azure-iot-typescript-latest) dos [polígono](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.data.polygon?view=azure-iot-typescript-latest) com [PolygonProperties](https://docs.microsoft.com/javascript/api/azure-maps-control/models.polygonproperties?view=azure-iot-typescript-latest) como sua propriedade de recurso. Utilize `new atlas.data.Feature(new atlas.data.Polygon())` para criar um polígono e definir as respetivas propriedades. Fornece coordenadas ordenadas do caminho de polígono no construtor do polígono.

Uma camada de polígonos é uma matriz de polígonos. O último bloco de código utiliza [addPolygons](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.map?view=azure-iot-typescript-latest#addpolygons) função da classe map para adicionar a camada de polígonos ao mapa e definir as respetivas propriedades. Ver as propriedades de uma camada de polígonos no [PolygonLayerOptions](https://docs.microsoft.com/javascript/api/azure-maps-control/models.polygonlayeroptions?view=azure-iot-typescript-latest).

## <a name="next-steps"></a>Passos Seguintes

Para obter mais exemplos de código adicionar a seus mapas, veja os artigos seguintes:

> [!div class="nextstepaction"]
> [Adicionar HTML personalizado](./map-add-custom-html.md)

> [!div class="nextstepaction"]
> [Mostrar resultados da pesquisa](./map-search-location.md)
