---
title: Adicionar uma forma com mapas do Azure | Microsoft Docs
description: Como adicionar uma forma a um mapa JavaScript
author: jingjing-z
ms.author: jinzh
ms.date: 07/29/2019
ms.topic: conceptual
ms.service: azure-maps
services: azure-maps
manager: ''
ms.custom: codepen
ms.openlocfilehash: 0696eba4f3cca7beedc2efcda0182ab82b3d69d9
ms.sourcegitcommit: 3877b77e7daae26a5b367a5097b19934eb136350
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 07/30/2019
ms.locfileid: "68638706"
---
# <a name="add-a-shape-to-a-map"></a>Adicionar uma forma a um mapa

Este artigo mostra como renderizar geometrias no mapa usando camadas de linha e polígono. O SDK da Web do Azure Maps também dá suporte à criação de geometrias de círculo, conforme definido no [esquema geojson estendido](extend-geojson.md#circle). Todas as geometrias de recurso também podem ser facilmente atualizadas se encapsuladas com a classe [Shape](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.shape?view=azure-iot-typescript-latest) .

<a id="addALine"></a>

## <a name="add-lines-to-the-map"></a>Adicionar linhas ao mapa

`LineString`e `MultiLineString` os recursos são usados para representar caminhos e contornos no mapa.

### <a name="add-a-line"></a>Adicionar uma linha

<iframe height='500' scrolling='no' title='Adicionar uma linha a um mapa' src='//codepen.io/azuremaps/embed/qomaKv/?height=500&theme-id=0&default-tab=js,result&embed-version=2&editable=true' frameborder='no' allowtransparency='true' allowfullscreen='true' style='width: 100%;'>Consulte a caneta <a href='https://codepen.io/azuremaps/pen/qomaKv/'>Adicionar uma linha a um mapa</a> pelo Azure Maps (<a href='https://codepen.io/azuremaps'>@azuremaps</a>) em <a href='https://codepen.io'>CodePen</a>.
</iframe>

O primeiro bloco de código no código acima constrói um objeto Map. Você pode ver [criar um mapa](./map-create.md) para obter instruções.

No segundo bloco de código, um objeto de fonte de dados é criado usando a classe [DataSource](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.source.datasource?view=azure-iot-typescript-latest) . Um [](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.data.linestring?view=azure-iot-typescript-latest) objeto LineString é criado e adicionado à fonte de dados.

Um [LineLayer](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.layer.linelayer?view=azure-iot-typescript-latest) renderiza objetos de linha encapsulados na [fonte de fontes](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.source.datasource?view=azure-iot-typescript-latest). O último bloco de código cria e adiciona uma camada de linha ao mapa. Consulte Propriedades de uma camada de linha em [LineLayerOptions](/javascript/api/azure-maps-control/atlas.linelayeroptions?view=azure-iot-typescript-latest). A fonte de dados e a camada de linha são criadas e adicionadas ao mapa dentro do [manipulador de eventos](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.map?view=azure-iot-typescript-latest#events) para garantir que a linha seja exibida depois que o mapa for totalmente carregado.

### <a name="add-symbols-along-a-line"></a>Adicionar símbolos ao longo de uma linha

Este exemplo mostra como adicionar ícones de seta ao longo de uma linha no mapa. Ao usar uma camada de símbolo, defina a opção "posicionamento" como "linha", isso irá renderizar os símbolos ao longo da linha e girar os ícones (0 graus = direita).

<br/>

<iframe height="500" style="width: 100%;" scrolling="no" title="Mostrar a seta ao longo da linha" src="//codepen.io/azuremaps/embed/drBJwX/?height=500&theme-id=0&default-tab=js,result" frameborder="no" allowtransparency="true" allowfullscreen="true">
Consulte a <a href='https://codepen.io/azuremaps/pen/drBJwX/'>seta de mostrar caneta ao longo da linha</a> pelo<a href='https://codepen.io/azuremaps'>@azuremaps</a>Azure Maps () em <a href='https://codepen.io'>CodePen</a>.
</iframe>

### <a name="line-stroke-gradient"></a>Adicionar um gradiente de traço a uma linha

Além de ser capaz de aplicar uma única cor de traço a uma linha, você também pode preencher uma linha com um gradiente de cores para mostrar a transição de um segmento de linha para o próximo. Por exemplo, gradientes de linha podem ser usados para representar alterações ao longo do tempo e distância, ou temperaturas diferentes em uma linha de objetos conectada. Para aplicar esse recurso a uma linha, a fonte de dados deve ter a `lineMetrics` opção definida como true e, em seguida, uma expressão de gradiente de cor pode ser passada para a `strokeColor` opção da linha. A expressão de gradiente de traço precisa `['line-progress']` fazer referência à expressão de dados que expõe as métricas de linha calculadas para a expressão.

<br/>

<iframe height="500" style="width: 100%;" scrolling="no" title="Linha com gradiente de traço" src="//codepen.io/azuremaps/embed/wZwWJZ/?height=500&theme-id=0&default-tab=js,result" frameborder="no" allowtransparency="true" allowfullscreen="true">
Veja a linha de caneta <a href='https://codepen.io/azuremaps/pen/wZwWJZ/'>com gradiente de traço</a> pelo<a href='https://codepen.io/azuremaps'>@azuremaps</a>Azure Maps () em <a href='https://codepen.io'>CodePen</a>.
</iframe>

### <a name="customize-a-line-layer"></a>Personalizar uma camada de linha

A camada de linha várias opções de estilo. Aqui está uma ferramenta para experimentá-las.

<br/>

<iframe height='700' scrolling='no' title='Opções de camada de linha' src='//codepen.io/azuremaps/embed/GwLrgb/?height=700&theme-id=0&default-tab=result' frameborder='no' allowtransparency='true' allowfullscreen='true' style='width: 100%;'>Consulte as <a href='https://codepen.io/azuremaps/pen/GwLrgb/'>Opções da camada de linha</a> de caneta pelo<a href='https://codepen.io/azuremaps'>@azuremaps</a>Azure Maps () em <a href='https://codepen.io'>CodePen</a>.
</iframe>

<a id="addAPolygon"></a>

## <a name="add-a-polygon-to-the-map"></a>Adicionar um polígono ao mapa

`Polygon`e `MultiPolygon` os recursos geralmente são usados para representar uma área em um mapa. 

### <a name="use-a-polygon-layer"></a>Usar uma camada de polígono 

Uma camada de polígono renderiza a área de um polígono. 

<iframe height='500' scrolling='no' title='Adicionar um polígono a um mapa ' src='//codepen.io/azuremaps/embed/yKbOvZ/?height=500&theme-id=0&default-tab=js,result&embed-version=2&editable=true' frameborder='no' allowtransparency='true' allowfullscreen='true' style='width: 100%;'>Consulte a caneta <a href='https://codepen.io/azuremaps/pen/yKbOvZ/'>Adicionar um polígono a um mapa</a> pelo Azure Maps (<a href='https://codepen.io/azuremaps'>@azuremaps</a>) em <a href='https://codepen.io'>CodePen</a>.
</iframe>

No código acima, o primeiro bloco de código constrói um objeto de mapa. Você pode ver [criar um mapa](./map-create.md) para obter instruções.

No segundo bloco de código, um objeto de fonte de dados é criado usando a classe [DataSource](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.source.datasource?view=azure-iot-typescript-latest) . Um [polígono](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.data.polygon?view=azure-iot-typescript-latest) é criado a partir de uma matriz de coordenadas e adicionado à fonte de dados. 

Um [PolygonLayer](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.layer.polygonlayer?view=azure-iot-typescript-latest) renderiza os dados encapsulados na [fonte](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.source.datasource?view=azure-iot-typescript-latest) da dados no mapa. O último bloco de código cria e adiciona uma camada de polígono ao mapa. Consulte Propriedades de uma camada de polígono em [PolygonLayerOptions](/javascript/api/azure-maps-control/atlas.polygonlayeroptions?view=azure-iot-typescript-latest). A fonte de dados e a camada de polígono são criadas e adicionadas ao mapa dentro do [manipulador de eventos](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.map?view=azure-iot-typescript-latest#events) para garantir que o polígono seja exibido depois que o mapa for totalmente carregado.

### <a name="use-a-polygon-and-line-layer-together"></a>Usar um polígono e uma camada de linha juntas

Uma camada de linha pode ser usada para renderizar o contorno de um polígono. 

<iframe height='500' scrolling='no' title='Polígono e camada de linha para adicionar polígono' src='//codepen.io/azuremaps/embed/aRyEPy/?height=500&theme-id=0&default-tab=js,result&embed-version=2&editable=true' frameborder='no' allowtransparency='true' allowfullscreen='true' style='width: 100%;'>Consulte o polígono da caneta <a href='https://codepen.io/azuremaps/pen/aRyEPy/'>e a camada de linha para adicionar polígono</a> pelo<a href='https://codepen.io/azuremaps'>@azuremaps</a>mapas do Azure () em <a href='https://codepen.io'>CodePen</a>.
</iframe>

No código acima, o primeiro bloco de código constrói um objeto de mapa. Você pode ver [criar um mapa](./map-create.md) para obter instruções.

No segundo bloco de código, um objeto de fonte de dados é criado usando a classe [DataSource](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.source.datasource?view=azure-iot-typescript-latest) . Um [polígono](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.data.polygon?view=azure-iot-typescript-latest) é criado a partir de uma matriz de coordenadas e adicionado à fonte de dados. 

Um [PolygonLayer](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.layer.polygonlayer?view=azure-iot-typescript-latest) renderiza os dados encapsulados na [fonte](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.source.datasource?view=azure-iot-typescript-latest) da dados no mapa. Consulte Propriedades de uma camada de polígono em [PolygonLayerOptions](/javascript/api/azure-maps-control/atlas.polygonlayeroptions?view=azure-iot-typescript-latest). Um [LineLayer](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.layer.linelayer?view=azure-iot-typescript-latest) é uma matriz de linhas. Consulte Propriedades de uma camada de linha em [LineLayerOptions](/javascript/api/azure-maps-control/atlas.linelayeroptions?view=azure-iot-typescript-latest). O terceiro bloco de código cria as camadas de polígono e de linha.

O último bloco de código adiciona as camadas de polígono e de linha ao mapa. A fonte de dados e as camadas são criadas e adicionadas ao mapa dentro do [manipulador de eventos](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.map?view=azure-iot-typescript-latest#events) para garantir que o polígono seja exibido depois que o mapa for totalmente carregado.

> [!TIP]
> As camadas de linha por padrão renderizarão as coordenadas de polígonos, bem como as linhas de uma fonte de dados. Para limitar a camada de modo que ela só processe os recursos de LineString `filter` , defina a propriedade da `['==', ['geometry-type'], 'LineString']` camada `['any', ['==', ['geometry-type'], 'LineString'], ['==', ['geometry-type'], 'MultiLineString']]` como ou se você quiser incluir recursos MultiLineString também.

### <a name="fill-a-polygon-with-a-pattern"></a>Preencher um polígono com um padrão

Além de preencher um polígono com uma cor, um padrão de imagem também pode ser usado. Carregue um padrão de imagem na imagem de mapas recursos de Sprite e, em seguida, `fillPattern` faça referência a essa imagem com a propriedade da camada de polígono.

<br/>

<iframe height="500" style="width: 100%;" scrolling="no" title="Padrão de preenchimento do polígono" src="//codepen.io/azuremaps/embed/JzQpYX/?height=500&theme-id=0&default-tab=js,result" frameborder="no" allowtransparency="true" allowfullscreen="true">
Consulte o <a href='https://codepen.io/azuremaps/pen/JzQpYX/'>padrão de preenchimento do polígono</a> da caneta pelo<a href='https://codepen.io/azuremaps'>@azuremaps</a>Azure Maps () em <a href='https://codepen.io'>CodePen</a>.
</iframe>

### <a name="customize-a-polygon-layer"></a>Personalizar uma camada de polígono

A camada de polígono tem apenas algumas opções de estilo. Aqui está uma ferramenta para experimentá-las.

<br/>

<iframe height='700' scrolling='no' title='LXvxpg' src='//codepen.io/azuremaps/embed/LXvxpg/?height=700&theme-id=0&default-tab=result' frameborder='no' allowtransparency='true' allowfullscreen='true' style='width: 100%;'>Consulte a <a href='https://codepen.io/azuremaps/pen/LXvxpg/'>LXvxpg</a> de caneta por mapas do<a href='https://codepen.io/azuremaps'>@azuremaps</a>Azure () em <a href='https://codepen.io'>CodePen</a>.
</iframe>

<a id="addACircle"></a>

## <a name="add-a-circle-to-the-map"></a>Adicionar um círculo ao mapa

O Azure Maps usa uma versão estendida do esquema geojson que fornece uma definição para círculos, conforme observado [aqui](extend-geojson.md#circle). Um círculo pode `Point` ser renderizado no mapa criando um recurso que tem uma `subType` Propriedade com um valor de `"Circle"` e uma `radius` propriedade que tem um número que representa o raio em metros. Por exemplo:

```javascript
{
    "type": "Feature",
    "geometry": {
        "type": "Point",
        "coordinates": [-122.126986, 47.639754]
    },
    "properties": {
        "subType": "Circle",
        "radius": 100
    }
}  
```

O SDK da Web do Azure Maps `Point` converte esses `Polygon` recursos em recursos nos bastidores e pode ser renderizado no mapa usando as camadas de polígono e linha, conforme mostrado aqui.

<iframe height='500' scrolling='no' title='Adicionar um círculo a um mapa' src='//codepen.io/azuremaps/embed/PRmzJX/?height=500&theme-id=0&default-tab=js,result&embed-version=2&editable=true' frameborder='no' allowtransparency='true' allowfullscreen='true' style='width: 100%;'>Consulte a caneta <a href='https://codepen.io/azuremaps/pen/PRmzJX/'>Adicionar um círculo a um mapa</a> pelo Azure Maps (<a href='https://codepen.io/azuremaps'>@azuremaps</a>) em <a href='https://codepen.io'>CodePen</a>.
</iframe>

O primeiro bloco de código no código acima constrói um objeto Map. Você pode ver [criar um mapa](./map-create.md) para obter instruções.

No segundo bloco de código, um objeto de fonte de dados é criado usando a classe [DataSource](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.source.datasource?view=azure-iot-typescript-latest) . Um círculo é um [recurso](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.data.feature?view=azure-iot-typescript-latest) do [ponto](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.data.point?view=azure-iot-typescript-latest) e tem uma `subType` propriedade definida como `"Circle"` e um `radius` valor de propriedade em metros. Quando um recurso de ponto com `subType` um `"Circle"` de é adicionado a uma fonte de dados, ele é convertido em um polígono circular dentro do mapa.

Um [PolygonLayer](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.layer.polygonlayer?view=azure-iot-typescript-latest) renderiza os dados encapsulados na [fonte](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.source.datasource?view=azure-iot-typescript-latest) da dados no mapa. O último bloco de código cria e adiciona uma camada de polígono ao mapa. Consulte Propriedades de uma camada de polígono em [PolygonLayerOptions](/javascript/api/azure-maps-control/atlas.polygonlayeroptions?view=azure-iot-typescript-latest). A fonte de dados e a camada de polígono são criadas e adicionadas ao mapa dentro do [manipulador de eventos](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.map?view=azure-iot-typescript-latest#events) para garantir que o círculo seja exibido depois que o mapa for totalmente carregado.

## <a name="make-a-geometry-easy-to-update"></a>Facilitar a atualização de uma geometria

Uma `Shape` classe envolve uma [geometria](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.data.geometry?view=azure-iot-typescript-latest) ou um [recurso](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.data.feature?view=azure-iot-typescript-latest) e torna mais fácil atualizá-las e mantê-las.
`new Shape(data: Feature<data.Geometry, any>)`Constrói um objeto Shape e o inicializa com o recurso especificado.

<br/>

<iframe height='500' scrolling='no' title='Atualizar propriedades da forma' src='//codepen.io/azuremaps/embed/ZqMeQY/?height=500&theme-id=0&default-tab=js,result&embed-version=2&editable=true' frameborder='no' allowtransparency='true' allowfullscreen='true' style='width: 100%;'>Consulte as <a href='https://codepen.io/azuremaps/pen/ZqMeQY/'>Propriedades da forma de atualização</a> da caneta por<a href='https://codepen.io/azuremaps'>@azuremaps</a>mapas do Azure () em <a href='https://codepen.io'>CodePen</a>.
</iframe>

O primeiro bloco de código acima constrói um objeto de mapa. Você pode ver [criar um mapa](./map-create.md) para obter instruções.

Um ponto é um [recurso](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.data.feature?view=azure-iot-typescript-latest) de [apontar](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.data.point?view=azure-iot-typescript-latest) a classe. O segundo bloco de código inicializa o valor RADIUS para o elemento slider de HTML e, em seguida, constrói e encapsula um objeto Point em um objeto de classe [Shape](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.shape?view=azure-iot-typescript-latest) .

O terceiro bloco de código cria uma função que usa o valor do elemento slider de intervalo HTML e altera o valor RADIUS usando o método [AddProperty](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.shape?view=azure-iot-typescript-latest) da classe Shape.

No quarto bloco de código, um objeto de fonte de dados é criado usando a classe [DataSource](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.source.datasource?view=azure-iot-typescript-latest) . O ponto é então adicionado à fonte de dados.

Um [PolygonLayer](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.layer.polygonlayer?view=azure-iot-typescript-latest) renderiza os dados encapsulados na [fonte](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.source.datasource?view=azure-iot-typescript-latest) da dados no mapa. O terceiro bloco de código cria uma camada de polígono. Consulte Propriedades de uma camada de polígono em [PolygonLayerOptions](/javascript/api/azure-maps-control/atlas.polygonlayeroptions?view=azure-iot-typescript-latest). A fonte de dados, o manipulador de eventos de clique e a camada de polígono são criados e adicionados ao mapa dentro do [manipulador de eventos](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.map?view=azure-iot-typescript-latest#events) para garantir que o ponto seja exibido depois que o mapa for totalmente carregado.

## <a name="next-steps"></a>Passos Seguintes

Para obter mais exemplos de código a serem adicionados aos seus mapas, consulte os seguintes artigos:

> [!div class="nextstepaction"]
> [Usar expressões de estilo controladas por dados](data-driven-style-expressions-web-sdk.md)
