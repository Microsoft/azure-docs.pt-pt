---
title: Criar uma fonte de dados no Azure Maps | Microsoft Docs
description: Como criar uma fonte de dados e usá-la com o SDK da Web do Azure Maps.
author: rbrundritt
ms.author: richbrun
ms.date: 08/08/2019
ms.topic: conceptual
ms.service: azure-maps
services: azure-maps
manager: ''
ms.custom: codepen
ms.openlocfilehash: c225aa5f821925247d27890e9ee75f3eac4d9eb9
ms.sourcegitcommit: 62bd5acd62418518d5991b73a16dca61d7430634
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 08/13/2019
ms.locfileid: "68977355"
---
# <a name="create-a-data-source"></a>Criar uma origem de dados

O SDK da Web do Azure Maps armazena dados em fontes de dados que otimizam os dados para consulta e renderização. Atualmente, há dois tipos de fontes de dados:

**Fonte de dados geojson**

Uma fonte de dados baseada em geojson pode carregar e armazenar dados localmente `DataSource` usando a classe. Os dados geojson podem ser criados ou criados manualmente usando as classes auxiliares no namespace do [Atlas. Data](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.data) . A `DataSource` classe fornece funções para importar arquivos geojson locais ou remotos. Arquivos geojson remotos devem ser hospedados em um ponto de extremidade habilitado para CORs. A `DataSource` classe fornece funcionalidade para dados de ponto de cluster. 

**Origem do bloco de vetor**

Uma fonte de bloco de vetor descreve como acessar uma camada de bloco de vetor e pode ser criada usando a classe [VectorTileSource](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.source.vectortilesource) . O Azure Maps se alinha com a [especificação de bloco de vetor Mapbox](https://github.com/mapbox/vector-tile-spec), que é um padrão aberto. As camadas de bloco de vetor são semelhantes às camadas de bloco, no entanto, em vez de cada bloco sendo uma imagem rasterizada, eles são um arquivo compactado (formato PBF) que contém dados de mapa de vetor e uma ou mais camadas que podem ser renderizadas e estilizadas no cliente com base no estilo de cada camada. Os dados em um bloco de vetor contêm recursos geográficos na forma de pontos, linhas e polígonos. Há várias vantagens de camadas de bloco de vetor em camadas de bloco rasterizadas;

 - Um tamanho de arquivo de um bloco de vetor normalmente é muito menor do que um bloco rasterizado equivalente. Dessa forma, menos largura de banda é usada, o que significa latência mais baixa e um mapa mais rápido. Isso cria uma melhor experiência do usuário.
 - Como os blocos de vetor são renderizados no cliente, eles podem se adaptar à resolução do dispositivo em que estão sendo exibidos. Isso permite que os mapas renderizados pareçam muito mais bem definidos e com rótulos de cristal claro. 
 - A alteração do estilo dos dados nos mapas de vetor não exige o download dos dados novamente, pois o novo estilo pode ser aplicado ao cliente. Por outro lado, alterar o estilo de uma camada de bloco de varredura normalmente requer o carregamento de blocos do servidor que têm o novo estilo aplicado a eles.
 - Como os dados são entregues na forma de vetor, há menos processamento do lado do servidor necessário para preparar os dados, o que significa que os dados mais novos podem ser disponibilizados mais rapidamente.

Todas as camadas que usam uma fonte de vetor devem `sourceLayer` especificar um valor. 

Depois de criadas, as fontes de dados podem ser adicionadas ao `map.sources` mapa por meio da propriedade, que é um [sourcemanager](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.sourcemanager). O código a seguir mostra como criar um `DataSource` e adicioná-lo ao mapa.

```javascript
//Create a data source and add it to the map.
var dataSource = new atlas.source.DataSource();
map.sources.add(dataSource);
```

## <a name="connecting-a-data-source-to-a-layer"></a>Conectando uma fonte de dados a uma camada

Os dados são renderizados no mapa usando camadas de renderização. Uma única fonte de dados pode ser referenciada por uma ou mais camadas de renderização. As camadas de renderização a seguir exigem que uma fonte de dados seja religada:

- [Camada de bolha](map-add-bubble-layer.md) – renderiza dados de ponto como círculos em escala no mapa.
- [Camada de símbolo](map-add-pin.md) – renderiza dados de ponto como ícones e/ou texto.
- [Camada do mapa de calor](map-add-heat-map-layer.md) – renderiza dados de ponto como um mapa de calor de densidade.
- [Camada de linha](map-add-shape.md) – pode ser usada para renderizar a linha e ou a estrutura de polígonos. 
- [Camada de polígono](map-add-shape.md) – preenche a área de um polígono com uma cor sólida ou padrão de imagem.

O código a seguir mostra como criar uma fonte de dados, adicioná-la ao mapa e conectá-la a uma camada de bolha e, em seguida, importar dados do ponto geojson de um local remoto para ele. 

```javascript
//Create a data source and add it to the map.
var datasource = new atlas.source.DataSource();
map.sources.add(datasource);

//Create a layer that defines how to render points in the data source and add it to the map.
map.layers.add(new atlas.layer.BubbleLayer(datasource));

//Load the earthquake data.
datasource.importDataFromUrl('https://earthquake.usgs.gov/earthquakes/feed/v1.0/summary/significant_month.geojson');
```

Há camadas de renderização adicionais que não se conectam a essas fontes de dados, mas carregam os dados que elas renderizam diretamente. 

- [Camada de imagem](map-add-image-layer.md) – sobrepõe uma única imagem na parte superior do mapa e associa seus cantos a um conjunto de coordenadas especificadas.
- [Camada de bloco](map-add-tile-layer.md) – sobrepõe uma camada de bloco de rasterização na parte superior do mapa.

## <a name="one-data-source-with-multiple-layers"></a>Uma fonte de dados com várias camadas

Várias camadas podem ser conectadas a uma única fonte de dados. Isso pode parecer estranho, mas há muitos cenários diferentes em que isso se torna útil. Veja, por exemplo, o cenário de criação de uma experiência de desenho de polígono. Ao permitir que um usuário desenhe um polígono, devemos renderizar a área de polígono de preenchimento, pois o usuário está adicionando pontos ao mapa. A adição de uma linha com estilo que descreve o polígono facilitará a visualização das bordas do polígono à medida que ele está sendo desenhado. Por fim, adicionar algum tipo de alça, como um PIN ou marcador, acima de cada posição no polígono, facilitaria a edição de cada posição individual. Aqui está uma imagem que demonstra esse cenário.

![Mapa mostrando várias camadas Renderizando dados de uma única fonte de dados](media/create-data-source-web-sdk/multiple-layers-one-datasource.png)

Para realizar esse cenário na maioria das plataformas de mapeamento, você precisaria criar um objeto Polygon, um objeto line e um PIN para cada posição no polígono. À medida que o polígono é modificado, você precisa atualizar manualmente a linha e os Pins, o que pode se tornar complexo rapidamente.

Com o Azure Maps, tudo o que você precisa é de um único polígono em uma fonte de dados, conforme mostrado no código abaixo.

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

## <a name="next-steps"></a>Passos Seguintes

Saiba mais sobre as classes e os métodos usados neste artigo:

> [!div class="nextstepaction"]
> [DataSource](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.source.datasource?view=azure-maps-typescript-latest)

> [!div class="nextstepaction"]
> [DataSourceOptions](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.datasourceoptions?view=azure-maps-typescript-latest)

> [!div class="nextstepaction"]
> [VectorTileSource](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.source.vectortilesource?view=azure-maps-typescript-latest)

> [!div class="nextstepaction"]
> [VectorTileSourceOptions](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.vectortilesourceoptions?view=azure-maps-typescript-latest)

Consulte os artigos a seguir para obter mais exemplos de código para adicionar aos seus mapas:

> [!div class="nextstepaction"]
> [Adicionar um pop-up](map-add-popup.md)

> [!div class="nextstepaction"]
> [Usar expressões de estilo controladas por dados](data-driven-style-expressions-web-sdk.md)

> [!div class="nextstepaction"]
> [Adicionar uma camada de símbolo](map-add-pin.md)

> [!div class="nextstepaction"]
> [Adicionar uma camada de bolha](map-add-bubble-layer.md)

> [!div class="nextstepaction"]
> [Adicionar uma camada de linha](map-add-line-layer.md)

> [!div class="nextstepaction"]
> [Adicionar uma camada de polígono](map-add-shape.md)

> [!div class="nextstepaction"]
> [Adicionar um mapa de calor](map-add-heat-map-layer.md)

> [!div class="nextstepaction"]
> [Exemplos de código](https://docs.microsoft.com/samples/browse/?products=azure-maps)