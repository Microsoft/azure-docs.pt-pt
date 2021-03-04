---
title: Adicione uma camada de linha a um mapa | Microsoft Azure Maps
description: Saiba como adicionar linhas aos mapas. Veja exemplos que usam o Azure Maps Web SDK para adicionar camadas de linha aos mapas e para personalizar linhas com símbolos e gradientes de cor.
author: rbrundritt
ms.author: richbrun
ms.date: 08/08/2019
ms.topic: conceptual
ms.service: azure-maps
services: azure-maps
manager: ''
ms.custom: codepen, devx-track-js
ms.openlocfilehash: fedf1bc37661100404106859d3aef52b3eddf06d
ms.sourcegitcommit: f3ec73fb5f8de72fe483995bd4bbad9b74a9cc9f
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/04/2021
ms.locfileid: "102048142"
---
# <a name="add-a-line-layer-to-the-map"></a>Adicione uma camada de linha ao mapa

Uma camada de linha pode ser usada para renderizar `LineString` e apresentar como caminhos ou rotas no `MultiLineString` mapa. Uma camada de linha também pode ser usada para tornar o contorno `Polygon` e `MultiPolygon` as características. Uma fonte de dados está ligada a uma camada de linha para fornecer-lhe dados para renderizar. 

> [!TIP]
> As camadas de linha por padrão tornarão as coordenadas dos polígonos, bem como as linhas numa fonte de dados. Para limitar a camada de tal forma que apenas torna as funcionalidades lineString definir a `filter` propriedade da camada para ou se você deseja incluir também `['==', ['geometry-type'], 'LineString']` `['any', ['==', ['geometry-type'], 'LineString'], ['==', ['geometry-type'], 'MultiLineString']]` funcionalidades MultiLineString.

O código que se segue mostra como criar uma linha. Adicione a linha a uma fonte de dados e, em seguida, torne-a com uma camada de linha utilizando a classe [LineLayer.](/javascript/api/azure-maps-control/atlas.layer.linelayer)

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

Abaixo está a amostra completa do código de execução da funcionalidade acima.

<br/>

<iframe height='500' scrolling='no' title='Adicione uma linha a um mapa' src='//codepen.io/azuremaps/embed/qomaKv/?height=500&theme-id=0&default-tab=js,result&embed-version=2&editable=true' frameborder='no' loading="lazy" allowtransparency='true' allowfullscreen='true' style='width: 100%;'>Ver a Caneta <a href='https://codepen.io/azuremaps/pen/qomaKv/'>Adicione uma linha a um mapa</a> de Azure Maps <a href='https://codepen.io/azuremaps'>@azuremaps</a> () no <a href='https://codepen.io'>CodePen</a>.
</iframe>

As camadas de linha podem ser modeladas usando [lineLayerOptions](/javascript/api/azure-maps-control/atlas.linelayeroptions) e [usar expressões de estilo orientadas por dados.](data-driven-style-expressions-web-sdk.md)

## <a name="add-symbols-along-a-line"></a>Adicione símbolos ao longo de uma linha

Esta amostra mostra como adicionar ícones de seta ao longo de uma linha no mapa. Quando utilizar uma camada de símbolo, deite a opção "colocação" para "line". Esta opção irá tornar os símbolos ao longo da linha e rodar os ícones (0 graus = à direita).

<br/>

<iframe height="500" style="width: 100%;" scrolling="no" title="Mostrar seta ao longo da linha" src="//codepen.io/azuremaps/embed/drBJwX/?height=500&theme-id=0&default-tab=js,result&editable=true" frameborder='no' loading="lazy" loading="lazy" allowtransparency="true" allowfullscreen="true">
Veja a <a href='https://codepen.io/azuremaps/pen/drBJwX/'>seta</a> Pen Show ao longo da linha por Azure Maps <a href='https://codepen.io/azuremaps'>@azuremaps</a> () no <a href='https://codepen.io'>CodePen</a>.
</iframe>

> [!TIP]
> A Azure Maps web SDK fornece vários modelos de imagem personalizáveis que você pode usar com a camada de símbolo. Para obter mais informações, consulte o documento [como usar modelos de imagem.](how-to-use-image-templates-web-sdk.md)

<a name="line-stroke-gradient"></a>

## <a name="add-a-stroke-gradient-to-a-line"></a>Adicione um gradiente de curso a uma linha

Pode aplicar uma cor de um único traçado numa linha. Também pode preencher uma linha com um gradiente de cores para mostrar a transição de um segmento de linha para o segmento de linha seguinte. Por exemplo, os gradientes de linha podem ser usados para representar mudanças ao longo do tempo e da distância, ou diferentes temperaturas através de uma linha de objetos conectados. Para aplicar esta funcionalidade a uma linha, a fonte de dados deve ter a `lineMetrics` opção definida para `true` , e então uma expressão de gradiente de cor pode ser passada para a `strokeColor` opção da linha. A expressão do gradiente de curso tem de fazer referência à expressão de `['line-progress']` dados que expõe as métricas de linha calculadas à expressão.

<br/>

<iframe height="500" style="width: 100%;" scrolling="no" title="Linha com Gradiente de Curso" src="//codepen.io/azuremaps/embed/wZwWJZ/?height=500&theme-id=0&default-tab=js,result&editable=true" frameborder='no' loading="lazy" loading="lazy" allowtransparency="true" allowfullscreen="true">
Consulte a Linha pen <a href='https://codepen.io/azuremaps/pen/wZwWJZ/'>com O Gradiente de Curso</a> por Azure Maps <a href='https://codepen.io/azuremaps'>@azuremaps</a> () no <a href='https://codepen.io'>CodePen</a>.
</iframe>

## <a name="customize-a-line-layer"></a>Personalize uma camada de linha

A camada Line tem várias opções de estilo. Aqui está uma ferramenta para experimentá-los.

<br/>

<iframe height='700' scrolling='no' title='Opções de camada de linha' src='//codepen.io/azuremaps/embed/GwLrgb/?height=700&theme-id=0&default-tab=result' frameborder='no' loading="lazy" allowtransparency='true' allowfullscreen='true' style='width: 100%;'>Consulte as <a href='https://codepen.io/azuremaps/pen/GwLrgb/'>opções de camada de linha de caneta</a> por Azure Maps <a href='https://codepen.io/azuremaps'>@azuremaps</a> () no <a href='https://codepen.io'>CodePen</a>.
</iframe>

## <a name="next-steps"></a>Passos seguintes

Saiba mais sobre as aulas e métodos utilizados neste artigo:

> [!div class="nextstepaction"]
> [LineLayer](/javascript/api/azure-maps-control/atlas.layer.linelayer) 

> [!div class="nextstepaction"]
> [LineLayerOptions](/javascript/api/azure-maps-control/atlas.linelayeroptions)

Consulte os seguintes artigos para obter mais amostras de código para adicionar aos seus mapas:

> [!div class="nextstepaction"]
> [Criar uma origem de dados](create-data-source-web-sdk.md)

> [!div class="nextstepaction"]
> [Adicionar um pop-up](map-add-popup.md)

> [!div class="nextstepaction"]
> [Utilizar expressões de estilo com base em dados](data-driven-style-expressions-web-sdk.md)

> [!div class="nextstepaction"]
> [Como utilizar modelos de imagem](how-to-use-image-templates-web-sdk.md)

> [!div class="nextstepaction"]
> [Adicionar uma camada de polígonos](map-add-shape.md)