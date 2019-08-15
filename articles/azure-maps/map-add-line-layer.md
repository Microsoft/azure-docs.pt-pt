---
title: Adicionar uma camada de linha ao Azure Maps | Microsoft Docs
description: Como adicionar uma camada de linha ao SDK da Web do Azure Maps.
author: rbrundritt
ms.author: richbrun
ms.date: 08/08/2019
ms.topic: conceptual
ms.service: azure-maps
services: azure-maps
manager: ''
ms.custom: codepen
ms.openlocfilehash: f07e36d82c9044a212cda8173df9fe0a9544393a
ms.sourcegitcommit: 62bd5acd62418518d5991b73a16dca61d7430634
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 08/13/2019
ms.locfileid: "68977329"
---
# <a name="add-a-line-layer-to-the-map"></a>Adicionar uma camada de linha ao mapa

Uma camada de linha pode ser usada para `LineString` processar `MultiLineString` e recursos como caminhos ou rotas no mapa. Uma camada de linha também pode ser usada para renderizar a `Polygon` estrutura `MultiPolygon` de tópicos e os recursos. Uma fonte de dados é conectada a uma camada de linha para fornecer dados a serem renderizados. 

> [!TIP]
> As camadas de linha por padrão renderizarão as coordenadas de polígonos, bem como as linhas de uma fonte de dados. Para limitar a camada de modo que ela só processe os recursos de LineString `filter` , defina a propriedade da `['==', ['geometry-type'], 'LineString']` camada `['any', ['==', ['geometry-type'], 'LineString'], ['==', ['geometry-type'], 'MultiLineString']]` como ou se você quiser incluir recursos MultiLineString também.

O código a seguir mostra como criar uma linha, adicioná-la a uma fonte de dados e renderizá-la com uma camada de linha usando a classe [LineLayer](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.layer.linelayer?view=azure-iot-typescript-latest) .

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

Este exemplo mostra como adicionar ícones de seta ao longo de uma linha no mapa. Ao usar uma camada de símbolo, defina a opção "posicionamento" como "linha", isso irá renderizar os símbolos ao longo da linha e girar os ícones (0 graus = direita).

<br/>

<iframe height="500" style="width: 100%;" scrolling="no" title="Mostrar a seta ao longo da linha" src="//codepen.io/azuremaps/embed/drBJwX/?height=500&theme-id=0&default-tab=js,result" frameborder="no" allowtransparency="true" allowfullscreen="true">
Consulte a <a href='https://codepen.io/azuremaps/pen/drBJwX/'>seta de mostrar caneta ao longo da linha</a> pelo<a href='https://codepen.io/azuremaps'>@azuremaps</a>Azure Maps () em <a href='https://codepen.io'>CodePen</a>.
</iframe>

> [!TIP]
> O SDK da Web do Azure Maps fornece vários modelos de imagem personalizáveis que você pode usar com a camada de símbolo. Para obter mais informações, consulte o documento [como usar modelos de imagem](how-to-use-image-templates-web-sdk.md) .

<a name="line-stroke-gradient"></a>

## <a name="add-a-stroke-gradient-to-a-line"></a>Adicionar um gradiente de traço a uma linha

Além de ser capaz de aplicar uma única cor de traço a uma linha, você também pode preencher uma linha com um gradiente de cores para mostrar a transição de um segmento de linha para o próximo. Por exemplo, gradientes de linha podem ser usados para representar alterações ao longo do tempo e distância, ou temperaturas diferentes em uma linha de objetos conectada. Para aplicar esse recurso a uma linha, a fonte de dados deve ter a `lineMetrics` opção definida como true e, em seguida, uma expressão de gradiente de cor pode ser passada para a `strokeColor` opção da linha. A expressão de gradiente de traço precisa `['line-progress']` fazer referência à expressão de dados que expõe as métricas de linha calculadas para a expressão.

<br/>

<iframe height="500" style="width: 100%;" scrolling="no" title="Linha com gradiente de traço" src="//codepen.io/azuremaps/embed/wZwWJZ/?height=500&theme-id=0&default-tab=js,result" frameborder="no" allowtransparency="true" allowfullscreen="true">
Veja a linha de caneta <a href='https://codepen.io/azuremaps/pen/wZwWJZ/'>com gradiente de traço</a> pelo<a href='https://codepen.io/azuremaps'>@azuremaps</a>Azure Maps () em <a href='https://codepen.io'>CodePen</a>.
</iframe>

## <a name="customize-a-line-layer"></a>Personalizar uma camada de linha

A camada de linha várias opções de estilo. Aqui está uma ferramenta para experimentá-las.

<br/>

<iframe height='700' scrolling='no' title='Opções de camada de linha' src='//codepen.io/azuremaps/embed/GwLrgb/?height=700&theme-id=0&default-tab=result' frameborder='no' allowtransparency='true' allowfullscreen='true' style='width: 100%;'>Consulte as <a href='https://codepen.io/azuremaps/pen/GwLrgb/'>Opções da camada de linha</a> de caneta pelo<a href='https://codepen.io/azuremaps'>@azuremaps</a>Azure Maps () em <a href='https://codepen.io'>CodePen</a>.
</iframe>

## <a name="next-steps"></a>Passos Seguintes

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