---
title: Adicionar uma camada de linha a um mapa | Mapas do Microsoft Azure
description: Neste artigo, você aprenderá a adicionar uma camada de linha a um mapa usando o SDK da Web do Microsoft Azure Maps.
author: rbrundritt
ms.author: richbrun
ms.date: 08/08/2019
ms.topic: conceptual
ms.service: azure-maps
services: azure-maps
manager: ''
ms.custom: codepen
ms.openlocfilehash: 8503b12be628fe7d5651221c9d0379bee3e292bd
ms.sourcegitcommit: fa6fe765e08aa2e015f2f8dbc2445664d63cc591
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 02/01/2020
ms.locfileid: "76933472"
---
# <a name="add-a-line-layer-to-the-map"></a>Adicionar uma camada de linha ao mapa

Uma camada de linha pode ser usada para renderizar `LineString` e `MultiLineString` recursos como caminhos ou rotas no mapa. Uma camada de linha também pode ser usada para processar a estrutura de tópicos de `Polygon` e `MultiPolygon` recursos. Uma fonte de dados está ligada a uma camada de linha para fornecer-lhe dados para renderizar. 

> [!TIP]
> As camadas de linha por padrão renderizarão as coordenadas de polígonos, bem como as linhas de uma fonte de dados. Para limitar a camada de tal forma que apenas torna as funcionalidades do LineString definir a propriedade `filter` da camada para `['==', ['geometry-type'], 'LineString']` ou `['any', ['==', ['geometry-type'], 'LineString'], ['==', ['geometry-type'], 'MultiLineString']]` se quiser incluir também as funcionalidades MultiLineString.

O código que se segue mostra como criar uma linha. Adicione a linha a uma fonte de dados e, em seguida, renderizá-la com uma camada de linha utilizando a classe [LineLayer.](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.layer.linelayer?view=azure-iot-typescript-latest)

```javascript
//Create a data source and add it to the map.
var dataSource = new atlas.source.DataSource();
map.sources.add(dataSource);

//Create a line and add it to the data source.
dataSource.add(new atlas.data.LineString([[-73.972340, 40.743270], [-74.004420, 40.756800]]));
  
//Create a line layer to render the line to the map.
map.layers.add(new atlas.layer.LineLayer(dataSource, null, {
    strokeColor: 'blue',
    strokeWidth: 5
}));
```

Abaixo está o exemplo de código completo em execução da funcionalidade acima.

<br/>

<iframe height='500' scrolling='no' title='Adicionar uma linha a um mapa' src='//codepen.io/azuremaps/embed/qomaKv/?height=500&theme-id=0&default-tab=js,result&embed-version=2&editable=true' frameborder='no' allowtransparency='true' allowfullscreen='true' style='width: 100%;'>Consulte a caneta <a href='https://codepen.io/azuremaps/pen/qomaKv/'>Adicionar uma linha a um mapa</a> pelo Azure Maps (<a href='https://codepen.io/azuremaps'>@azuremaps</a>) em <a href='https://codepen.io'>CodePen</a>.
</iframe>

Camadas de linha podem ser estilizadas usando [LineLayerOptions](/javascript/api/azure-maps-control/atlas.linelayeroptions?view=azure-iot-typescript-latest) e [usam expressões de estilo controladas por dados](data-driven-style-expressions-web-sdk.md).

## <a name="add-symbols-along-a-line"></a>Adicionar símbolos ao longo de uma linha

Este exemplo mostra como adicionar ícones de seta ao longo de uma linha no mapa. Quando utilizar uma camada de símbolo, delineie a opção "colocação" para "linha". Esta opção irá renderizar os símbolos ao longo da linha e rodar os ícones (0 graus = direita).

<br/>

<iframe height="500" style="width: 100%;" scrolling="no" title="Mostrar a seta ao longo da linha" src="//codepen.io/azuremaps/embed/drBJwX/?height=500&theme-id=0&default-tab=js,result" frameborder="no" allowtransparency="true" allowfullscreen="true">
Consulte a <a href='https://codepen.io/azuremaps/pen/drBJwX/'>seta de mostrar caneta ao longo da linha</a> pelo Azure Maps (<a href='https://codepen.io/azuremaps'>@azuremaps</a>) em <a href='https://codepen.io'>CodePen</a>.
</iframe>

> [!TIP]
> O SDK da Web do Azure Maps fornece vários modelos de imagem personalizáveis que você pode usar com a camada de símbolo. Para obter mais informações, consulte o documento [como usar modelos de imagem](how-to-use-image-templates-web-sdk.md) .

<a name="line-stroke-gradient"></a>

## <a name="add-a-stroke-gradient-to-a-line"></a>Adicionar um gradiente de traço a uma linha

Pode aplicar uma única cor de traçado numa linha. Também pode preencher uma linha com um gradiente de cores para mostrar a transição de um segmento de linha para o segmento de linha seguinte. Por exemplo, gradientes de linha podem ser usados para representar alterações ao longo do tempo e distância, ou temperaturas diferentes em uma linha de objetos conectada. Para aplicar esse recurso a uma linha, a fonte de dados deve ter a opção `lineMetrics` definida como true e, em seguida, uma expressão de gradiente de cor pode ser passada para a `strokeColor` opção da linha. A expressão gradiente de acidente vascular cerebral tem de fazer referência à expressão `['line-progress']` de dados que expõe as métricas de linha calculadas à expressão.

<br/>

<iframe height="500" style="width: 100%;" scrolling="no" title="Linha com gradiente de traço" src="//codepen.io/azuremaps/embed/wZwWJZ/?height=500&theme-id=0&default-tab=js,result" frameborder="no" allowtransparency="true" allowfullscreen="true">
Veja a linha de caneta <a href='https://codepen.io/azuremaps/pen/wZwWJZ/'>com gradiente de traço</a> pelo Azure Maps (<a href='https://codepen.io/azuremaps'>@azuremaps</a>) em <a href='https://codepen.io'>CodePen</a>.
</iframe>

## <a name="customize-a-line-layer"></a>Personalizar uma camada de linha

A camada de linha tem várias opções de estilo. Aqui está uma ferramenta para experimentá-las.

<br/>

<iframe height='700' scrolling='no' title='Opções de camada de linha' src='//codepen.io/azuremaps/embed/GwLrgb/?height=700&theme-id=0&default-tab=result' frameborder='no' allowtransparency='true' allowfullscreen='true' style='width: 100%;'>Consulte as <a href='https://codepen.io/azuremaps/pen/GwLrgb/'>Opções da camada de linha</a> de caneta pelo mapas do Azure (<a href='https://codepen.io/azuremaps'>@azuremaps</a>) em <a href='https://codepen.io'>CodePen</a>.
</iframe>

## <a name="next-steps"></a>Passos seguintes

Saiba mais sobre as classes e os métodos usados neste artigo:

> [!div class="nextstepaction"]
> [LineLayer](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.layer.linelayer?view=azure-iot-typescript-latest) 

> [!div class="nextstepaction"]
> [LineLayerOptions](/javascript/api/azure-maps-control/atlas.linelayeroptions?view=azure-iot-typescript-latest)

Consulte os artigos a seguir para obter mais exemplos de código para adicionar aos seus mapas:

> [!div class="nextstepaction"]
> [Criar uma fonte de dados](create-data-source-web-sdk.md)

> [!div class="nextstepaction"]
> [Adicionar um pop-up](map-add-popup.md)

> [!div class="nextstepaction"]
> [Usar expressões de estilo controladas por dados](data-driven-style-expressions-web-sdk.md)

> [!div class="nextstepaction"]
> [Como usar modelos de imagem](how-to-use-image-templates-web-sdk.md)

> [!div class="nextstepaction"]
> [Adicionar uma camada de polígono](map-add-shape.md)
