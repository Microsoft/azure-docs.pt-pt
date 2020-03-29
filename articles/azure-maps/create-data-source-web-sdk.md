---
title: Criar uma fonte de dados para um mapa Microsoft Azure Maps
description: Neste artigo, você vai aprender a criar uma fonte de dados e adicioná-lo a um mapa usando o Microsoft Azure Maps Web SDK.
author: rbrundritt
ms.author: richbrun
ms.date: 08/08/2019
ms.topic: conceptual
ms.service: azure-maps
services: azure-maps
manager: cpendle
ms.custom: codepen
ms.openlocfilehash: 1675d63fd3a65beda46042f4a78535bb4e066e62
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/27/2020
ms.locfileid: "77190238"
---
# <a name="create-a-data-source"></a>Criar uma origem de dados

O Azure Maps Web SDK armazena dados em fontes de dados. A utilização de fontes de dados otimiza as operações de dados para consulta e renderização. Atualmente existem dois tipos de fontes de dados:

**Fonte de dados da GeoJSON**

Uma carga de fonte de dados baseada em `DataSource` GeoJSON e armazenar dados localmente usando a classe. Os dados geoJSON podem ser criados manualmente ou criados utilizando as classes auxiliares no espaço de nome [atlas.data.](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.data) A `DataSource` classe fornece funções para importar ficheiros GeoJSON locais ou remotos. Os ficheiros GeoJSON remotos devem ser hospedados num ponto final ativado por CORs. A `DataSource` classe fornece funcionalidade para dados de pontos de agrupamento. E, os dados podem ser facilmente `DataSource` adicionados, removidos e atualizados com a classe.


> [!TIP]
> Digamos que quer substituir todos os `DataSource`dados em . Se fizer chamadas `clear` para `add` as funções então, o mapa pode voltar a renderizar duas vezes, o que pode causar um pequeno atraso. Em vez `setShapes` disso, utilize a função, que removerá e substituirá todos os dados na fonte de dados e apenas desencadeará uma única reprodução do mapa.

**Fonte de azulejo seletor**

Uma fonte de azulejos vetoriais descreve como aceder a uma camada de azulejos vetoriais. Use a classe [VectorTileSource](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.source.vectortilesource) para instantaneamente uma fonte de azulejos vetoriais. As camadas de azulejos vetores são semelhantes às camadas de azulejos, mas não são as mesmas. Uma camada de azulejo é uma imagem mais raster. Camadas de azulejos vetoriais são um ficheiro comprimido, em formato PBF. Este ficheiro comprimido contém dados do mapa vetorial, e uma ou mais camadas. O ficheiro pode ser renderizado e modelado no cliente, com base no estilo de cada camada. Os dados em um azulejo vetorial contêm características geográficas na forma de pontos, linhas e polígonos. Existem várias vantagens em usar camadas de azulejos vetores em vez de camadas de azulejos mais raster:

 - Um tamanho de arquivo de um azulejo vetorial é tipicamente muito menor do que um azulejo raster equivalente. Como tal, é utilizada menos largura de banda. Significa menor latência, um mapa mais rápido e uma melhor experiência do utilizador.
 - Uma vez que os azulejos vetoriais são renderizados no cliente, adaptam-se à resolução do dispositivo em que estão a ser exibidos. Como resultado, os mapas renderizados parecem mais bem definidos, com etiquetas cristalinas.
 - Mudar o estilo dos dados nos mapas vetores não requer o descarregamento dos dados novamente, uma vez que o novo estilo pode ser aplicado no cliente. Em contraste, mudar o estilo de uma camada de azulejo ster tipicamente requer carregar azulejos do servidor e depois aplicar o novo estilo.
 - Uma vez que os dados são entregues em forma vetorial, há menos processamento do lado do servidor necessário para preparar os dados. Como resultado, os dados mais recentes podem ser disponibilizados mais rapidamente.

Todas as camadas que usam `sourceLayer` uma fonte vetorial devem especificar um valor.

Uma vez criadas, as fontes `map.sources` de dados podem ser adicionadas ao mapa através da propriedade, que é um [SourceManager](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.sourcemanager). O código seguinte mostra `DataSource` como criar um e adicioná-lo ao mapa.

```javascript
//Create a data source and add it to the map.
var dataSource = new atlas.source.DataSource();
map.sources.add(dataSource);
```

O Azure Maps adere à especificação de [azulejos do vetor mapbox,](https://github.com/mapbox/vector-tile-spec)um padrão aberto.

## <a name="connecting-a-data-source-to-a-layer"></a>Ligar uma fonte de dados a uma camada

Os dados são renderizados no mapa utilizando camadas de renderização. Uma única fonte de dados pode ser referenciada por uma ou mais camadas de renderização. As seguintes camadas de renderização requerem uma fonte de dados:

- [Camada de bolha](map-add-bubble-layer.md) - torna os dados de pontocomo círculos escalados no mapa.
- [Camada de símbolo](map-add-pin.md) - torna os dados de pontos como ícones ou texto.
- [Camada](map-add-heat-map-layer.md) de mapa de calor - torna os dados de ponto como um mapa de calor de densidade.
- [Camada de linha](map-add-shape.md) - renderizar uma linha e ou render o contorno dos políginos. 
- Camada de [polígono](map-add-shape.md) - preenche a área de um polígono com uma cor sólida ou padrão de imagem.

O código que se segue mostra como criar uma fonte de dados, adicioná-la ao mapa e ligá-la a uma camada de bolha. E então, importar dados de ponto sonorizadores GeoJSON de uma localização remota para a fonte de dados. 

```javascript
//Create a data source and add it to the map.
var datasource = new atlas.source.DataSource();
map.sources.add(datasource);

//Create a layer that defines how to render points in the data source and add it to the map.
map.layers.add(new atlas.layer.BubbleLayer(datasource));

//Load the earthquake data.
datasource.importDataFromUrl('https://earthquake.usgs.gov/earthquakes/feed/v1.0/summary/significant_month.geojson');
```

Existem camadas de renderização adicionais que não se ligam a estas fontes de dados, mas carregam diretamente os dados para renderização. 

- [Camada](map-add-image-layer.md) de imagem - sobrepõe uma única imagem em cima do mapa e liga os seus cantos a um conjunto de coordenadas especificadas.
- [Camada](map-add-tile-layer.md) de azulejo - sobrepõe uma camada de azulejo ster em cima do mapa.

## <a name="one-data-source-with-multiple-layers"></a>Uma fonte de dados com várias camadas

Várias camadas podem ser ligadas a uma única fonte de dados. Há muitos cenários diferentes em que esta opção é útil. Por exemplo, considere o cenário em que um utilizador desenha um polígono. Devemos renderizar e preencher a área do polígono à medida que o utilizador adiciona pontos ao mapa. A adição de uma linha de estilo para delinear o polígono facilita a ver as bordas do polígono, à medida que o utilizador desenha. Para editar convenientemente uma posição individual no polígono, podemos adicionar uma pega, como um pino ou um marcador, acima de cada posição.

![Mapa mostrando várias camadas prestando dados de uma única fonte de dados](media/create-data-source-web-sdk/multiple-layers-one-datasource.png)

Na maioria das plataformas de mapeamento, você precisaria de um objeto de polígono, um objeto de linha, e um pino para cada posição no polígono. À medida que o polígono é modificado, é necessário atualizar manualmente a linha e os pinos, que podem rapidamente tornar-se complexos.

Com o Azure Maps, tudo o que precisa é de um único polígono numa fonte de dados, como mostrado no código abaixo.

```javascript
//Create a data source and add it to the map.
var dataSource = new atlas.source.DataSource();
map.sources.add(dataSource);

//Create a polygon and add it to the data source.
dataSource.add(new atlas.data.Polygon([[[/* Coordinates for polygon */]]]));

//Create a polygon layer to render the filled in area of the polygon.
var polygonLayer = new atlas.layer.PolygonLayer(dataSource, 'myPolygonLayer', {
     fillColor: 'rgba(255,165,0,0.2)'
});

//Create a line layer for greater control of rendering the outline of the polygon.
var lineLayer = new atlas.layer.LineLayer(dataSource, 'myLineLayer', {
     color: 'orange',
     width: 2
});

//Create a bubble layer to render the vertices of the polygon as scaled circles.
var bubbleLayer = new atlas.layer.BubbleLayer(dataSource, 'myBubbleLayer', {
     color: 'orange',
     radius: 5,
     outlineColor: 'white',
     outlineWidth: 2
});

//Add all layers to the map.
map.layers.add([polygonLayer, lineLayer, bubbleLayer]);
```

## <a name="next-steps"></a>Passos seguintes

Saiba mais sobre as aulas e métodos utilizados neste artigo:

> [!div class="nextstepaction"]
> [DataSource](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.source.datasource?view=azure-maps-typescript-latest)

> [!div class="nextstepaction"]
> [DataSourceOptions](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.datasourceoptions?view=azure-maps-typescript-latest)

> [!div class="nextstepaction"]
> [VectorTileSource](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.source.vectortilesource?view=azure-maps-typescript-latest)

> [!div class="nextstepaction"]
> [VectorTileSourceOptions](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.vectortilesourceoptions?view=azure-maps-typescript-latest)

Consulte os seguintes artigos para obter mais amostras de código para adicionar aos seus mapas:

> [!div class="nextstepaction"]
> [Adicionar um pop-up](map-add-popup.md)

> [!div class="nextstepaction"]
> [Utilizar expressões de estilo com base em dados](data-driven-style-expressions-web-sdk.md)

> [!div class="nextstepaction"]
> [Adicionar uma camada de símbolo](map-add-pin.md)

> [!div class="nextstepaction"]
> [Adicionar uma camada de bolha](map-add-bubble-layer.md)

> [!div class="nextstepaction"]
> [Adicionar uma camada de linhas](map-add-line-layer.md)

> [!div class="nextstepaction"]
> [Adicionar uma camada de polígonos](map-add-shape.md)

> [!div class="nextstepaction"]
> [Adicione um mapa de calor](map-add-heat-map-layer.md)

> [!div class="nextstepaction"]
> [Amostras de código](https://docs.microsoft.com/samples/browse/?products=azure-maps)