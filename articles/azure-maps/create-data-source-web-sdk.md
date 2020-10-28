---
title: Criar uma fonte de dados para um mapa Microsoft Azure Maps
description: 'Descubra como criar uma fonte de dados para um mapa. Conheça as fontes de dados que o Azure Maps Web SDK utiliza: Fontes geoJSON e azulejos vetoriais.'
author: rbrundritt
ms.author: richbrun
ms.date: 08/08/2019
ms.topic: conceptual
ms.service: azure-maps
services: azure-maps
manager: cpendle
ms.custom: codepen, devx-track-js
ms.openlocfilehash: 9c82b74ffdc8672dc3d84a98a036c6083bc6c309
ms.sourcegitcommit: 4064234b1b4be79c411ef677569f29ae73e78731
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/28/2020
ms.locfileid: "92895924"
---
# <a name="create-a-data-source"></a>Criar uma origem de dados

O Azure Maps Web SDK armazena dados em fontes de dados. A utilização de fontes de dados otimiza as operações de dados para consulta e renderização. Atualmente existem dois tipos de fontes de dados:

- **GeoJSON fonte** : Gere dados de localização bruta em formato GeoJSON localmente. Bom para pequenos a médios conjuntos de dados (mais de centenas de milhares de formas).
- **Fonte de azulejos vetoriais** : Carrega dados formatados como azulejos vetoriais para a visão do mapa atual, com base no sistema de inclinação de mapas. Ideal para grandes e maciços conjuntos de dados (milhões ou biliões de formas).

## <a name="geojson-data-source"></a>Fonte de dados da GeoJSON

Uma carga de fonte de dados baseada em GeoJSON e armazena dados localmente usando a `DataSource` classe. Os dados da GeoJSON podem ser criados manualmente ou criados utilizando as classes de ajudantes no espaço de [nomes atlas.data.](/javascript/api/azure-maps-control/atlas.data) A `DataSource` classe fornece funções para importar ficheiros GeoJSON locais ou remotos. Os ficheiros GeoJSON remotos devem ser alojados num ponto final ativado por CORs. A `DataSource` classe fornece funcionalidade para os dados de ponto de agrupamento. E, os dados podem ser facilmente adicionados, removidos e atualizados com a `DataSource` classe. O código que se segue mostra como os dados da GeoJSON podem ser criados no Azure Maps.

```javascript
//Create raw GeoJSON object.
var rawGeoJson = {
     "type": "Feature",
     "geometry": {
         "type": "Point",
         "coordinates": [-100, 45]
     },
     "properties": {
         "custom-property": "value"
     }
};

//Create GeoJSON using helper classes (less error prone and less typing).
var geoJsonClass = new atlas.data.Feature(new atlas.data.Point([-100, 45]), {
    "custom-property": "value"
}); 
```

Uma vez criados, as fontes de dados podem ser adicionadas ao mapa através da `map.sources` propriedade, que é um [SourceManager](/javascript/api/azure-maps-control/atlas.sourcemanager). O código que se segue mostra como criar um `DataSource` e adicioná-lo ao mapa.

```javascript
//Create a data source and add it to the map.
var dataSource = new atlas.source.DataSource();
map.sources.add(dataSource);
```

O código que se segue mostra as diferentes formas de adicionar dados da GeoJSON a `DataSource` um .

```javascript
//GeoJsonData in the following code can be a single or array of GeoJSON features or geometries, a GeoJSON feature colleciton, or a single or array of atlas.Shape objects.

//Add geoJSON object to data source. 
dataSource.add(geoJsonData);

//Load geoJSON data from URL. URL should be on a CORs enabled endpoint.
dataSource.importDataFromUrl(geoJsonUrl);

//Overwrite all data in data source.
dataSource.setShapes(geoJsonData);
```

> [!TIP]
> Digamos que quer substituir todos os dados de uma `DataSource` . Se fizer chamadas para as funções do `clear` `add` então, o mapa pode voltar a renderizar duas vezes, o que pode causar um pouco de atraso. Em vez disso, utilize a `setShapes` função, que removerá e substituirá todos os dados na fonte de dados e apenas desencadeará uma única re-renderização do mapa.

## <a name="vector-tile-source"></a>Fonte de azulejos do vetor

Uma fonte de azulejos do vetor descreve como aceder a uma camada de azulejo vetorial. Use a classe [VectorTileSource](/javascript/api/azure-maps-control/atlas.source.vectortilesource) para instantaneaizar uma fonte de azulejos vetoriais. As camadas de azulejos vetoriais são semelhantes às camadas de azulejos, mas não são as mesmas. Uma camada de azulejo é uma imagem raster. As camadas de azulejos vetoriais são um ficheiro comprimido, em formato **PBF.** Este ficheiro comprimido contém dados de mapas de vetores e uma ou mais camadas. O ficheiro pode ser renderizado e modelado no cliente, com base no estilo de cada camada. Os dados de um azulejo vetorial contêm características geográficas sob a forma de pontos, linhas e polígonos. Existem várias vantagens de usar camadas de azulejos vetoriais em vez de camadas de azulejos rasteres:

 - Um tamanho de arquivo de um azulejo vetorial é tipicamente muito menor do que um azulejo raster equivalente. Como tal, é utilizada menos largura de banda. Significa menor latência, um mapa mais rápido, e uma melhor experiência de utilizador.
 - Uma vez que os azulejos vetoriais são renderizados no cliente, eles adaptam-se à resolução do dispositivo em que estão a ser exibidos. Como resultado, os mapas renderizados parecem mais bem definidos, com rótulos cristalinos.
 - Alterar o estilo dos dados nos mapas de vetores não requer o download dos dados novamente, uma vez que o novo estilo pode ser aplicado no cliente. Em contraste, mudar o estilo de uma camada de azulejo raster normalmente requer carregar azulejos do servidor e, em seguida, aplicar o novo estilo.
 - Uma vez que os dados são entregues em forma de vetor, há menos processamento do lado do servidor necessário para preparar os dados. Como resultado, os dados mais recentes podem ser disponibilizados mais rapidamente.

Azure Maps adere à [especificação do azulejo do vetor mapbox,](https://github.com/mapbox/vector-tile-spec)um padrão aberto. A Azure Maps fornece os seguintes serviços de azulejos vetoriais como parte da plataforma:

- Detalhes do formato de dados [de documentação](/rest/api/maps/renderv2/getmaptilepreview)de azulejos  |  [de estrada](https://developer.tomtom.com/maps-api/maps-api-documentation-vector/tile)
- Incidentes de [tráfego documentação](/rest/api/maps/traffic/gettrafficincidenttile)  |  [dados detalhes do formato de dados](https://developer.tomtom.com/traffic-api/traffic-api-documentation-traffic-incidents/vector-incident-tiles)
- Detalhes do formato de [dados de documentação](/rest/api/maps/traffic/gettrafficflowtile)de fluxo de tráfego  |  [data format details](https://developer.tomtom.com/traffic-api/traffic-api-documentation-traffic-flow/vector-flow-tiles)
- O Azure Maps Creator também permite que os azulejos vetores personalizados sejam criados e acedidos através do [Get Tile Render V2](/rest/api/maps/renderv2/getmaptilepreview)

> [!TIP]
> Ao utilizar azulejos de imagem vetor ou raster do Azure Maps, o serviço de prestação de dados com a web SDK, pode `atlas.microsoft.com` substituir-se pelo espaço reservado `{azMapsDomain}` . Este espaço reservado será substituído pelo mesmo domínio utilizado pelo mapa e anexará automaticamente os mesmos detalhes de autenticação também. Isto simplifica consideravelmente a autenticação com o serviço de prestação quando se utiliza a autenticação do Azure Ative Directory.

Para exibir dados de uma fonte de azulejos vetoriais no mapa, ligue a fonte a uma das camadas de renderização de dados. Todas as camadas que usam uma fonte vetorial devem especificar um `sourceLayer` valor nas opções. O código seguinte carrega o serviço de vetor de fluxo de tráfego Azure Maps como uma fonte de azulejo vetorial, em seguida, exibe-o em um mapa usando uma camada de linha. Esta fonte de azulejos vetoriais tem um único conjunto de dados na camada de origem chamada "Fluxo de tráfego". Os dados da linha neste conjunto de dados têm uma propriedade chamada `traffic_level` que é usada neste código para selecionar a cor e escalar o tamanho das linhas.

```javascript
//Create a vector tile source and add it to the map.
var datasource = new atlas.source.VectorTileSource(null, {
    tiles: ['https://{azMapsDomain}/traffic/flow/tile/pbf?api-version=1.0&style=relative&zoom={z}&x={x}&y={y}'],
    maxZoom: 22
});
map.sources.add(datasource);

//Create a layer for traffic flow lines.
var flowLayer = new atlas.layer.LineLayer(datasource, null, {
    //The name of the data layer within the data source to pass into this rendering layer.
    sourceLayer: 'Traffic flow',

    //Color the roads based on the traffic_level property. 
    strokeColor: [
        'interpolate',
        ['linear'],
        ['get', 'traffic_level'],
        0, 'red',
        0.33, 'orange',
        0.66, 'green'
    ],

    //Scale the width of roads based on the traffic_level property. 
    strokeWidth: [
        'interpolate',
        ['linear'],
        ['get', 'traffic_level'],
        0, 6,
        1, 1
    ]
});

//Add the traffic flow layer below the labels to make the map clearer.
map.layers.add(flowLayer, 'labels');
```

<br/>

<iframe height="500" style="width: 100%;" scrolling="no" title="Camada de linha de azulejos do vetor" src="https://codepen.io/azuremaps/embed/wvMXJYJ?height=500&theme-id=default&default-tab=js,result&editable=true" frameborder="no" allowtransparency="true" allowfullscreen="true">
Consulte a <a href='https://codepen.io/azuremaps/pen/wvMXJYJ'>camada de linha de azulejos pen vetor</a> por Azure Maps <a href='https://codepen.io/azuremaps'>@azuremaps</a> () no <a href='https://codepen.io'>CodePen</a>.
</iframe>

<br/>

## <a name="connecting-a-data-source-to-a-layer"></a>Ligação de uma fonte de dados a uma camada

Os dados são renderizados no mapa utilizando camadas de renderização. Uma única fonte de dados pode ser referenciada por uma ou mais camadas de renderização. As seguintes camadas de renderização requerem uma fonte de dados:

- [Camada de](map-add-bubble-layer.md) bolha - torna os dados de ponto como círculos escalonado no mapa.
- [Symbol layer](map-add-pin.md) - torna os dados de ponto como ícones ou texto.
- [Heat map layer](map-add-heat-map-layer.md) - torna os dados do ponto como um mapa de calor de densidade.
- [Camada de linha](map-add-shape.md) - torne uma linha e ou torne o contorno dos polígonos. 
- [Polygon layer](map-add-shape.md) - preenche a área de um polígono com uma cor sólida ou padrão de imagem.

O código que se segue mostra como criar uma fonte de dados, adicioná-la ao mapa e conectá-la a uma camada de bolha. E então, importar dados de pontos GeoJSON de uma localização remota para a fonte de dados. 

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

- [Camada de imagem](map-add-image-layer.md) - sobrepõe uma única imagem no topo do mapa e liga os seus cantos a um conjunto de coordenadas especificadas.
- [Camada de azulejo](map-add-tile-layer.md) - sobrepõe-se a uma camada de azulejo raster no topo do mapa.

## <a name="one-data-source-with-multiple-layers"></a>Uma fonte de dados com várias camadas

Várias camadas podem ser ligadas a uma única fonte de dados. Há muitos cenários diferentes em que esta opção é útil. Por exemplo, considere o cenário em que um utilizador desenha um polígono. Devemos renderizar e preencher a área do polígono à medida que o utilizador adiciona pontos ao mapa. Adicionar uma linha de estilo para delinear o polígono facilita a raridade de ver as bordas do polígono, à medida que o utilizador desenha. Para editar convenientemente uma posição individual no polígono, podemos adicionar uma pega, como um pino ou um marcador, acima de cada posição.

![Mapa mostrando várias camadas renderizando dados de uma única fonte de dados](media/create-data-source-web-sdk/multiple-layers-one-datasource.png)

Na maioria das plataformas de mapeamento, você precisaria de um objeto de polígono, um objeto de linha, e um pino para cada posição no polígono. À medida que o polígono é modificado, você precisaria atualizar manualmente a linha e os pinos, que podem rapidamente tornar-se complexos.

Com o Azure Maps, tudo o que precisa é de um único polígono numa fonte de dados, como mostra o código abaixo.

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
> [Fonte de Dados](/javascript/api/azure-maps-control/atlas.source.datasource)

> [!div class="nextstepaction"]
> [Opções DataSource](/javascript/api/azure-maps-control/atlas.datasourceoptions)

> [!div class="nextstepaction"]
> [VectorTileSource](/javascript/api/azure-maps-control/atlas.source.vectortilesource)

> [!div class="nextstepaction"]
> [Opções VectorTileSource](/javascript/api/azure-maps-control/atlas.vectortilesourceoptions)

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
> [Amostras de código](/samples/browse/?products=azure-maps)