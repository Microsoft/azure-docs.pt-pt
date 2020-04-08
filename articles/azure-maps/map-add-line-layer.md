---
title: Adicione uma camada de linha a um mapa Microsoft Azure Maps
description: Neste artigo, você aprenderá a adicionar uma camada de linha a um mapa usando o Microsoft Azure Maps Web SDK.
author: rbrundritt
ms.author: richbrun
ms.date: 08/08/2019
ms.topic: conceptual
ms.service: azure-maps
services: azure-maps
manager: ''
ms.custom: codepen
ms.openlocfilehash: c473be25907eb3a761fbccd598bb9b732e5be5b9
ms.sourcegitcommit: 6397c1774a1358c79138976071989287f4a81a83
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/07/2020
ms.locfileid: "80802353"
---
# <a name="add-a-line-layer-to-the-map"></a>Adicione uma camada de linha ao mapa

Uma camada de linha `LineString` pode `MultiLineString` ser usada para renderizar e características como caminhos ou rotas no mapa. Uma camada de linha também pode `Polygon` ser `MultiPolygon` usada para renderizar o contorno de e características. Uma fonte de dados está ligada a uma camada de linha para fornecer-lhe dados para renderizar. 

> [!TIP]
> As camadas de linha por padrão tornarão as coordenadas dos polígonos, bem como as linhas numa fonte de dados. Para limitar a camada de tal forma que `filter` apenas torna `['==', ['geometry-type'], 'LineString']` `['any', ['==', ['geometry-type'], 'LineString'], ['==', ['geometry-type'], 'MultiLineString']]` as funcionalidades lineString definir a propriedade da camada para ou se você quiser incluir funcionalidades MultiLineString também.

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

Abaixo está a amostra completa de código de execução da funcionalidade acima referida.

<br/>

<iframe height='500' scrolling='no' title='Adicione uma linha a um mapa' src='//codepen.io/azuremaps/embed/qomaKv/?height=500&theme-id=0&default-tab=js,result&embed-version=2&editable=true' frameborder='no' allowtransparency='true' allowfullscreen='true' style='width: 100%;'>Consulte a caneta <a href='https://codepen.io/azuremaps/pen/qomaKv/'>Adicione uma linha a um mapa</a> de Azure Maps ()<a href='https://codepen.io/azuremaps'>@azuremaps</a>em <a href='https://codepen.io'>CodePen</a>.
</iframe>

As camadas de linha podem ser modeladas utilizando [linelayerOptions](/javascript/api/azure-maps-control/atlas.linelayeroptions?view=azure-iot-typescript-latest) e [usar expressões de estilo orientadas por dados](data-driven-style-expressions-web-sdk.md).

## <a name="add-symbols-along-a-line"></a>Adicione símbolos ao longo de uma linha

Esta amostra mostra como adicionar ícones de seta ao longo de uma linha no mapa. Quando utilizar uma camada de símbolo, delineie a opção "colocação" para "linha". Esta opção irá renderizar os símbolos ao longo da linha e rodar os ícones (0 graus = direita).

<br/>

<iframe height="500" style="width: 100%;" scrolling="no" title="Mostrar seta ao longo da linha" src="//codepen.io/azuremaps/embed/drBJwX/?height=500&theme-id=0&default-tab=js,result&editable=true" frameborder="no" allowtransparency="true" allowfullscreen="true">
Veja a <a href='https://codepen.io/azuremaps/pen/drBJwX/'>seta</a> Pen Show ao<a href='https://codepen.io/azuremaps'>@azuremaps</a>longo da linha por Azure Maps () em <a href='https://codepen.io'>CodePen</a>.
</iframe>

> [!TIP]
> O Web SDK do Azure Maps fornece vários modelos de imagem personalizáveis que pode usar com a camada de símbolo. Para mais informações, consulte o documento [de como utilizar modelos de imagem.](how-to-use-image-templates-web-sdk.md)

<a name="line-stroke-gradient"></a>

## <a name="add-a-stroke-gradient-to-a-line"></a>Adicione um gradiente de traçado a uma linha

Pode aplicar uma única cor de traçado numa linha. Também pode preencher uma linha com um gradiente de cores para mostrar a transição de um segmento de linha para o segmento de linha seguinte. Por exemplo, os gradientes de linha podem ser usados para representar mudanças ao longo do tempo e distância, ou temperaturas diferentes através de uma linha de objetos conectados. Para aplicar esta funcionalidade a uma linha, a `lineMetrics` fonte de dados deve ter a opção definida `strokeColor` para ser verdadeira, e então uma expressão gradiente de cor pode ser passada para a opção da linha. A expressão gradiente de `['line-progress']` traçado tem que referenciar a expressão de dados que expõe as métricas de linha calculadas à expressão.

<br/>

<iframe height="500" style="width: 100%;" scrolling="no" title="Linha com Gradiente de Acidente Vascular Cerebral" src="//codepen.io/azuremaps/embed/wZwWJZ/?height=500&theme-id=0&default-tab=js,result&editable=true" frameborder="no" allowtransparency="true" allowfullscreen="true">
Veja a Linha da Caneta <a href='https://codepen.io/azuremaps/pen/wZwWJZ/'>com Gradiente de Traçado</a> por Azure Maps ()<a href='https://codepen.io/azuremaps'>@azuremaps</a>em <a href='https://codepen.io'>CodePen</a>.
</iframe>

## <a name="customize-a-line-layer"></a>Personalize uma camada de linha

A camada de linha tem várias opções de estilo. Aqui está uma ferramenta para experimentá-los.

<br/>

<iframe height='700' scrolling='no' title='Opções de camada de linha' src='//codepen.io/azuremaps/embed/GwLrgb/?height=700&theme-id=0&default-tab=result' frameborder='no' allowtransparency='true' allowfullscreen='true' style='width: 100%;'>Consulte as <a href='https://codepen.io/azuremaps/pen/GwLrgb/'>opções</a> de camada<a href='https://codepen.io/azuremaps'>@azuremaps</a>de linha de caneta por Azure Maps () no <a href='https://codepen.io'>CodePen</a>.
</iframe>

## <a name="next-steps"></a>Passos seguintes

Saiba mais sobre as aulas e métodos utilizados neste artigo:

> [!div class="nextstepaction"]
> [Linhacamada](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.layer.linelayer?view=azure-iot-typescript-latest) 

> [!div class="nextstepaction"]
> [Opções de Camadas de Linha](/javascript/api/azure-maps-control/atlas.linelayeroptions?view=azure-iot-typescript-latest)

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
