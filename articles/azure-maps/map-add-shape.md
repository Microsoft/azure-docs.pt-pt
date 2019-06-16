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
ms.openlocfilehash: f61c7a939902ee5d02b2e9ba896c7555968f9d0d
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 06/13/2019
ms.locfileid: "60769520"
---
# <a name="add-a-shape-to-a-map"></a>Adicionar uma forma para um mapa

Este artigo mostra-lhe como processar geometrias no mapa utilizando as camadas de linhas e polígonos. O SDK de Web de mapas do Azure também suporta a criação de geometrias do círculo, conforme definido na [esquema de GeoJSON expandido](extend-geojson.md#circle). Todas as geometrias de recurso também podem ser facilmente atualizadas se encapsuladas com a [forma](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.shape?view=azure-iot-typescript-latest) classe.

<a id="addALine"></a>

## <a name="add-lines-to-the-map"></a>Adicionar linhas ao mapa

`LineString` e `MultiLineString` recursos são usados para representar os caminhos e os contornos no mapa.

### <a name="add-a-line"></a>Adicionar uma linha

<iframe height='500' scrolling='no' title='Adicionar uma linha para um mapa' src='//codepen.io/azuremaps/embed/qomaKv/?height=500&theme-id=0&default-tab=js,result&embed-version=2&editable=true' frameborder='no' allowtransparency='true' allowfullscreen='true' style='width: 100%;'>Consulte a caneta <a href='https://codepen.io/azuremaps/pen/qomaKv/'>adicionar uma linha para um mapa</a> ao Azure Maps (<a href='https://codepen.io/azuremaps'>@azuremaps</a>) na <a href='https://codepen.io'>CodePen</a>.
</iframe>

O primeiro bloco de código no código acima constrói um objeto de mapa. Pode ver [criar um mapa](./map-create.md) para obter instruções.

O segundo bloco de código, um objeto de origem de dados é criado utilizando o [origem de dados](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.source.datasource?view=azure-iot-typescript-latest) classe. R [LineString](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.data.linestring?view=azure-iot-typescript-latest) objeto é criado e adicionado à origem de dados.

R [LineLayer](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.layer.linelayer?view=azure-iot-typescript-latest) composições linha objetos encapsulados no [DataSource](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.source.datasource?view=azure-iot-typescript-latest). O último bloco de código cria e adiciona uma camada de linhas ao mapa. Ver as propriedades de uma camada de linha em [LineLayerOptions](/javascript/api/azure-maps-control/atlas.linelayeroptions?view=azure-iot-typescript-latest). A origem de dados e a camada de linhas são criadas e adicionadas ao mapa dentro de [manipulador de eventos](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.map?view=azure-iot-typescript-latest#events) para se certificar de que a linha é apresentada depois que o mapa for totalmente carregada.

### <a name="add-symbols-along-a-line"></a>Adicionar símbolos ao longo de uma linha

Este exemplo mostra como adicionar os ícones de seta ao longo de uma linha no mapa. Quando utilizar uma camada de símbolo, definir a opção de "Colocação" como "linha", esta ação irá renderizar os símbolos ao longo da linha e girar os ícones de (0 graus = direito).

<br/>

<iframe height="500" style="width: 100%;" scrolling="no" title="Mostrar seta ao longo da linha" src="//codepen.io/azuremaps/embed/drBJwX/?height=500&theme-id=0&default-tab=js,result" frameborder="no" allowtransparency="true" allowfullscreen="true">
Consulte a caneta <a href='https://codepen.io/azuremaps/pen/drBJwX/'>Show seta ao longo de linha</a> ao Azure Maps (<a href='https://codepen.io/azuremaps'>@azuremaps</a>) na <a href='https://codepen.io'>CodePen</a>.
</iframe>

### <a name="line-stroke-gradient"></a> Adicionar um gradiente de traço para uma linha

Além de poder aplicar uma cor de traço único para uma linha também pode preencher uma linha com um gradiente de cores para mostrar a transição de um segmento de linha para a próxima. Por exemplo, gradientes de linha podem ser utilizados para representar as alterações ao longo do tempo e distância ou as temperaturas diferentes numa linha ligada de objetos. Para aplicar esta funcionalidade a uma linha, a origem de dados tem de ter o `lineMetrics` opção definido como true e, em seguida, uma expressão de gradação de cor pode ser passada para o `strokeColor` opção da linha. A expressão de gradação do traço tem a referência a `['line-progress']` expressão de dados que expõe as métricas de linha calculadas para a expressão.

<br/>

<iframe height="500" style="width: 100%;" scrolling="no" title="Linha de gradação do traço" src="//codepen.io/azuremaps/embed/wZwWJZ/?height=500&theme-id=0&default-tab=js,result" frameborder="no" allowtransparency="true" allowfullscreen="true">
Consulte a caneta <a href='https://codepen.io/azuremaps/pen/wZwWJZ/'>linha com gradiente de traço</a> através do Azure Maps (<a href='https://codepen.io/azuremaps'>@azuremaps</a>) na <a href='https://codepen.io'>CodePen</a>.
</iframe>

### <a name="customize-a-line-layer"></a>Personalizar uma camada de linha

A linha camada várias opções de estilo. Aqui está uma ferramenta de experimentá-las.

<br/>

<iframe height='700' scrolling='no' title='Opções da linha de camada' src='//codepen.io/azuremaps/embed/GwLrgb/?height=700&theme-id=0&default-tab=result' frameborder='no' allowtransparency='true' allowfullscreen='true' style='width: 100%;'>Consulte a caneta <a href='https://codepen.io/azuremaps/pen/GwLrgb/'>camada opções de linha</a> através do Azure Maps (<a href='https://codepen.io/azuremaps'>@azuremaps</a>) na <a href='https://codepen.io'>CodePen</a>.
</iframe>

<a id="addAPolygon"></a>

## <a name="add-a-polygon-to-the-map"></a>Adicionar um polígono ao mapa

`Polygon` e `MultiPolygon` funcionalidades, muitas vezes, são utilizadas para representar uma área num mapa. 

### <a name="use-a-polygon-layer"></a>Utilizar uma camada de polígonos 

Uma camada de polígonos renderiza a área de um polígono. 

<iframe height='500' scrolling='no' title='Adicionar um polígono para um mapa ' src='//codepen.io/azuremaps/embed/yKbOvZ/?height=500&theme-id=0&default-tab=js,result&embed-version=2&editable=true' frameborder='no' allowtransparency='true' allowfullscreen='true' style='width: 100%;'>Consulte a caneta <a href='https://codepen.io/azuremaps/pen/yKbOvZ/'>adicionar um polígono para um mapa </a> ao Azure Maps (<a href='https://codepen.io/azuremaps'>@azuremaps</a>) na <a href='https://codepen.io'>CodePen</a>.
</iframe>

No código acima, o primeiro bloco de código constrói um objeto de mapa. Pode ver [criar um mapa](./map-create.md) para obter instruções.

O segundo bloco de código, um objeto de origem de dados é criado utilizando o [origem de dados](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.source.datasource?view=azure-iot-typescript-latest) classe. R [polígono](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.data.polygon?view=azure-iot-typescript-latest) é criada a partir de uma matriz de coordenadas e adicionado à origem de dados. 

R [PolygonLayer](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.layer.polygonlayer?view=azure-iot-typescript-latest) processa os dados encapsulados no [DataSource](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.source.datasource?view=azure-iot-typescript-latest) no mapa. O último bloco de código cria e adiciona uma camada de polígonos ao mapa. Ver as propriedades de uma camada de polígonos no [PolygonLayerOptions](/javascript/api/azure-maps-control/atlas.polygonlayeroptions?view=azure-iot-typescript-latest). A origem de dados e a camada de polígonos são criadas e adicionadas ao mapa dentro de [manipulador de eventos](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.map?view=azure-iot-typescript-latest#events) para se certificar de que o polígono é apresentado depois que o mapa for totalmente carregada.

### <a name="use-a-polygon-and-line-layer-together"></a>Utilizar uma camada de polígonos e de linhas em conjunto

Uma camada de linha pode ser usada para processar a estrutura de um polígono. 

<iframe height='500' scrolling='no' title='Camada de polígonos e de linhas para adicionar o polígono' src='//codepen.io/azuremaps/embed/aRyEPy/?height=500&theme-id=0&default-tab=js,result&embed-version=2&editable=true' frameborder='no' allowtransparency='true' allowfullscreen='true' style='width: 100%;'>Consulte a caneta <a href='https://codepen.io/azuremaps/pen/aRyEPy/'>camada de polígonos e de linhas para adicionar o polígono</a> ao Azure Maps (<a href='https://codepen.io/azuremaps'>@azuremaps</a>) na <a href='https://codepen.io'>CodePen</a>.
</iframe>

No código acima, o primeiro bloco de código constrói um objeto de mapa. Pode ver [criar um mapa](./map-create.md) para obter instruções.

O segundo bloco de código, um objeto de origem de dados é criado utilizando o [origem de dados](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.source.datasource?view=azure-iot-typescript-latest) classe. R [polígono](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.data.polygon?view=azure-iot-typescript-latest) é criada a partir de uma matriz de coordenadas e adicionado à origem de dados. 

R [PolygonLayer](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.layer.polygonlayer?view=azure-iot-typescript-latest) processa os dados encapsulados no [DataSource](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.source.datasource?view=azure-iot-typescript-latest) no mapa. Ver as propriedades de uma camada de polígonos no [PolygonLayerOptions](/javascript/api/azure-maps-control/atlas.polygonlayeroptions?view=azure-iot-typescript-latest). R [LineLayer](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.layer.linelayer?view=azure-iot-typescript-latest) é uma matriz de linhas. Ver as propriedades de uma camada de linha em [LineLayerOptions](/javascript/api/azure-maps-control/atlas.linelayeroptions?view=azure-iot-typescript-latest). O terceiro bloco de código cria as camadas de polígonos e de linhas.

O último bloco de código adiciona as camadas de polígonos e de linhas ao mapa. A origem de dados e as camadas são criadas e adicionadas ao mapa dentro de [manipulador de eventos](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.map?view=azure-iot-typescript-latest#events) para se certificar de que o polígono é apresentado depois que o mapa for totalmente carregada.

> [!TIP]
> Camadas de linha por predefinição processará as coordenadas de polígonos, bem como as linhas numa origem de dados. Para limitar a camada de forma que ele só processa LineString funcionalidades conjunto a `filter` propriedade da camada para `['==', ['geometry-type'], 'LineString']` ou `['any', ['==', ['geometry-type'], 'LineString'], ['==', ['geometry-type'], 'MultiLineString']]` se desejar incluir funcionalidades de MultiLineString também.

### <a name="fill-a-polygon-with-a-pattern"></a>Preencha um polígono com um padrão

Para além de preenchimento de um polígono com uma cor de um padrão de imagem também pode ser utilizado. Carregar um padrão de imagem para os recursos de sprite mapas de imagem e, em seguida, fazer referência a esta imagem com o `fillPattern` propriedade da camada de polígonos.

<br/>

<iframe height="500" style="width: 100%;" scrolling="no" title="Padrão de preenchimento de polígono" src="//codepen.io/azuremaps/embed/JzQpYX/?height=500&theme-id=0&default-tab=js,result" frameborder="no" allowtransparency="true" allowfullscreen="true">
Consulte a caneta <a href='https://codepen.io/azuremaps/pen/JzQpYX/'>padrão de preenchimento de polígono</a> ao Azure Maps (<a href='https://codepen.io/azuremaps'>@azuremaps</a>) na <a href='https://codepen.io'>CodePen</a>.
</iframe>

### <a name="customize-a-polygon-layer"></a>Personalizar uma camada de polígonos

A camada de polígonos tem apenas algumas opções de estilo. Aqui está uma ferramenta de experimentá-las.

<br/>

<iframe height='700' scrolling='no' title='LXvxpg' src='//codepen.io/azuremaps/embed/LXvxpg/?height=700&theme-id=0&default-tab=result' frameborder='no' allowtransparency='true' allowfullscreen='true' style='width: 100%;'>Consulte a caneta <a href='https://codepen.io/azuremaps/pen/LXvxpg/'>LXvxpg</a> através do Azure Maps (<a href='https://codepen.io/azuremaps'>@azuremaps</a>) na <a href='https://codepen.io'>CodePen</a>.
</iframe>

<a id="addACircle"></a>

## <a name="add-a-circle-to-the-map"></a>Adicionar um círculo ao mapa

Mapas do Azure utiliza uma versão expandida do esquema GeoJSON que fornece uma definição para círculos, conforme observado [aqui](extend-geojson.md#circle). Um círculo pode ser composto no mapa através da criação de um `Point` funcionalidade que tenha uma `subType` propriedade com um valor de `"Circle"` e um `radius` propriedade que tem um número que representa o raio em metros. Por exemplo:

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

O SDK do Azure Maps Web converte estes `Pooint` funcionalidades em `Polygon` recursos nos bastidores e pode ser composto no mapa utilizando as camadas de polígonos e de linhas, como mostrado aqui.

<iframe height='500' scrolling='no' title='Adicione um círculo para um mapa' src='//codepen.io/azuremaps/embed/PRmzJX/?height=500&theme-id=0&default-tab=js,result&embed-version=2&editable=true' frameborder='no' allowtransparency='true' allowfullscreen='true' style='width: 100%;'>Consulte a caneta <a href='https://codepen.io/azuremaps/pen/PRmzJX/'>adicione um círculo para um mapa</a> ao Azure Maps (<a href='https://codepen.io/azuremaps'>@azuremaps</a>) na <a href='https://codepen.io'>CodePen</a>.
</iframe>

O primeiro bloco de código no código acima constrói um objeto de mapa. Pode ver [criar um mapa](./map-create.md) para obter instruções.

O segundo bloco de código, um objeto de origem de dados é criado utilizando o [origem de dados](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.source.datasource?view=azure-iot-typescript-latest) classe. Um círculo é um [funcionalidade](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.data.feature?view=azure-iot-typescript-latest) de [ponto](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.data.point?view=azure-iot-typescript-latest) e tem um `subType` propriedade definida como `"Circle"` e uma `radius` valor da propriedade em metros. Quando um recurso de ponto com um `subType` de `"Circle"` é adicionado a uma origem de dados, ele convertido num polígono circular no mapa.

R [PolygonLayer](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.layer.polygonlayer?view=azure-iot-typescript-latest) processa os dados encapsulados no [DataSource](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.source.datasource?view=azure-iot-typescript-latest) no mapa. O último bloco de código cria e adiciona uma camada de polígonos ao mapa. Ver as propriedades de uma camada de polígonos no [PolygonLayerOptions](/javascript/api/azure-maps-control/atlas.polygonlayeroptions?view=azure-iot-typescript-latest). A origem de dados e a camada de polígonos são criadas e adicionadas ao mapa dentro de [manipulador de eventos](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.map?view=azure-iot-typescript-latest#events) para se certificar de que o círculo é apresentado depois que o mapa for totalmente carregada.

## <a name="make-a-geometry-easy-to-update"></a>Fazer uma geometria de fácil de atualizar

R `Shape` classe inclui uma [Geometry](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.data.geometry?view=azure-iot-typescript-latest) ou [funcionalidade](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.data.feature?view=azure-iot-typescript-latest) e torna mais fácil de atualizar e mantê-los.
`new Shape(data: Feature<data.Geometry, any>)` constrói um objeto de forma e o inicializa com o recurso especificado.

<br/>

<iframe height='500' scrolling='no' title='Atualizar propriedades de forma' src='//codepen.io/azuremaps/embed/ZqMeQY/?height=500&theme-id=0&default-tab=js,result&embed-version=2&editable=true' frameborder='no' allowtransparency='true' allowfullscreen='true' style='width: 100%;'>Consulte a caneta <a href='https://codepen.io/azuremaps/pen/ZqMeQY/'>atualizar propriedades de forma</a> ao Azure Maps (<a href='https://codepen.io/azuremaps'>@azuremaps</a>) na <a href='https://codepen.io'>CodePen</a>.
</iframe>

O primeiro bloco de código acima constrói um objeto de mapa. Pode ver [criar um mapa](./map-create.md) para obter instruções.

É um ponto de um [funcionalidade](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.data.feature?view=azure-iot-typescript-latest) dos [ponto](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.data.point?view=azure-iot-typescript-latest) a classe. O segundo bloco de código inicializa o valor de radius para o elemento do controlo de deslize HTML e, em seguida, constrói e encapsula um objeto de ponto numa [forma](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.shape?view=azure-iot-typescript-latest) objeto da classe.

O terceiro bloco de código cria uma função que usa o valor do elemento de controlo de deslize de intervalo de HTML e altera o valor de radius usando a classe shape [addProperty](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.shape?view=azure-iot-typescript-latest) método.

No quarto bloco de código, é criado um objeto de origem de dados usando o [origem de dados](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.source.datasource?view=azure-iot-typescript-latest) classe. O ponto de, em seguida, é adicionado à origem de dados.

R [PolygonLayer](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.layer.polygonlayer?view=azure-iot-typescript-latest) processa os dados encapsulados no [DataSource](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.source.datasource?view=azure-iot-typescript-latest) no mapa. O terceiro bloco de código cria uma camada de polígonos. Ver as propriedades de uma camada de polígonos no [PolygonLayerOptions](/javascript/api/azure-maps-control/atlas.polygonlayeroptions?view=azure-iot-typescript-latest). A origem de dados, o manipulador de eventos de clique e a camada de polígonos são criadas e adicionadas ao mapa dentro de [manipulador de eventos](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.map?view=azure-iot-typescript-latest#events) para se certificar de que o ponto é apresentado depois que o mapa for totalmente carregada.

## <a name="next-steps"></a>Passos Seguintes

Para obter mais exemplos de código adicionar a seus mapas, veja os artigos seguintes:

> [!div class="nextstepaction"]
> [Utilizar expressões de estilo condicionada por dados](data-driven-style-expressions-web-sdk.md)
