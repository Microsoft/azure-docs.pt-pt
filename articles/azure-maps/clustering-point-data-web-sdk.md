---
title: Dados de pontos de agrupamento num mapa Microsoft Azure Maps
description: Saiba como agrupar dados de pontos nos mapas. Veja como usar o Azure Maps Web SDK para cluster de dados, reagir a eventos de rato de cluster e exibir agregados de cluster.
author: rbrundritt
ms.author: richbrun
ms.date: 07/29/2019
ms.topic: conceptual
ms.service: azure-maps
services: azure-maps
manager: cpendle
ms.custom: codepen, devx-track-js
ms.openlocfilehash: 9ecde4cb0c8a3bfe3dd8fb2edb59423838e0751c
ms.sourcegitcommit: 4064234b1b4be79c411ef677569f29ae73e78731
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/28/2020
ms.locfileid: "92889872"
---
# <a name="clustering-point-data"></a>Dados de pontos de agrupamento

Ao visualizar muitos pontos de dados no mapa, os pontos de dados podem sobrepor-se uns aos outros. A sobreposição pode causar a ilegível e difícil utilização do mapa. Os dados de ponto de agrupamento são o processo de combinar dados de pontos que estão próximos uns dos outros e representá-los no mapa como um único ponto de dados agrupados. À medida que o utilizador faz zoom no mapa, os clusters separam-se nos seus pontos de dados individuais. Quando trabalhar com um grande número de pontos de dados, utilize os processos de agrupamento para melhorar a sua experiência de utilizador.

<br/>

<iframe src="https://channel9.msdn.com/Shows/Internet-of-Things-Show/Clustering-point-data-in-Azure-Maps/player" width="960" height="540" allowFullScreen frameBorder="0"></iframe>

## <a name="enabling-clustering-on-a-data-source"></a>Habilitação de agrupamento numa fonte de dados

Ativar o agrupamento na `DataSource` classe definindo a `cluster` opção para a verdade. Definir `ClusterRadius` para selecionar pontos próximos e combiná-los em um cluster. O valor dos `ClusterRadius` pixéis está em pixels. Utilize `clusterMaxZoom` para especificar um nível de zoom para desativar a lógica de agrupamento. Aqui está um exemplo de como permitir o agrupamento numa fonte de dados.

```javascript
//Create a data source and enable clustering.
var datasource = new atlas.source.DataSource(null, {
    //Tell the data source to cluster point data.
    cluster: true,

    //The radius in pixels to cluster points together.
    clusterRadius: 45,

    //The maximum zoom level in which clustering occurs.
    //If you zoom in more than this, all points are rendered as symbols.
    clusterMaxZoom: 15
});
```

> [!TIP]
> Se dois pontos de dados estiverem próximos no terreno, é possível que o cluster nunca se desfaça, por muito perto que o utilizador se aproxime. Para resolver isto, pode definir a `clusterMaxZoom` opção de desativar a lógica de clustering e simplesmente exibir tudo.

Aqui estão os métodos adicionais que a `DataSource` classe prevê para o agrupamento:

| Método | Tipo de retorno | Descrição |
|--------|-------------|-------------|
| getClusterChildren (clusterId: número) | Prometa &lt; &lt; geometria de característica &lt; de matriz, qualquer &gt; \| forma&gt;&gt; | Recupera as crianças do aglomerado dado no próximo nível de zoom. Estas crianças podem ser uma combinação de formas e subclusters. Os subclusters serão funcionalidades com propriedades correspondentes a ClusteredProperties. |
| getClusterExpansionZoom (clusterId: número) | &lt;Número de promessa&gt; | Calcula um nível de zoom no qual o cluster começará a expandir-se ou a separar-se. |
| getClusterLeaves (clusterId: número, limite: número, offset: número) | Prometa &lt; &lt; geometria de característica &lt; de matriz, qualquer &gt; \| forma&gt;&gt; | Recupera todos os pontos num aglomerado. Defina o `limit` para devolver um subconjunto dos pontos e use a `offset` página para página através dos pontos. |

## <a name="display-clusters-using-a-bubble-layer"></a>Exibir aglomerados usando uma camada de bolha

Uma camada de bolha é uma ótima maneira de render pontos agrupados. Utilize expressões para escalar o raio e alterar a cor com base no número de pontos no cluster. Se apresentar clusters usando uma camada de bolha, então deve usar uma camada separada para tornar pontos de dados não aglomerados.

Para mostrar o tamanho do cluster em cima da bolha, use uma camada de símbolo com texto e não use um ícone.

<br/>

<iframe height="500" style="width: 100%;" scrolling="no" title="Agrupamento básico de camada de bolha" src="//codepen.io/azuremaps/embed/qvzRZY/?height=500&theme-id=0&default-tab=js,result&editable=true" frameborder="no" allowtransparency="true" allowfullscreen="true">
Consulte o <a href='https://codepen.io/azuremaps/pen/qvzRZY/'>agrupamento de camadas de bolha básica</a> pen por Azure Maps <a href='https://codepen.io/azuremaps'>@azuremaps</a> () no <a href='https://codepen.io'>CodePen</a>.
</iframe>

## <a name="display-clusters-using-a-symbol-layer"></a>Display clusters usando uma camada de símbolo

Ao visualizar pontos de dados, a camada de símbolos esconde automaticamente símbolos que se sobrepõem uns aos outros para garantir uma interface de utilizador mais limpa. Este comportamento padrão pode ser indesejável se quiser mostrar a densidade dos pontos de dados no mapa. No entanto, estas definições podem ser alteradas. Para exibir todos os símbolos, desate a `allowOverlap` opção da propriedade das camadas symbol `iconOptions` para `true` . 

Utilize o agrupamento para mostrar a densidade dos pontos de dados, mantendo uma interface de utilizador limpa. A amostra abaixo mostra-lhe como adicionar símbolos personalizados e representar clusters e pontos de dados individuais usando a camada de símbolo.

<br/>

<iframe height="500" style="width: 100%;" scrolling="no" title="Camada de símbolo agrupado" src="//codepen.io/azuremaps/embed/Wmqpzz/?height=500&theme-id=0&default-tab=js,result&editable=true" frameborder="no" allowtransparency="true" allowfullscreen="true">
Consulte a <a href='https://codepen.io/azuremaps/pen/Wmqpzz/'>camada de símbolo agrupado</a> por Azure Maps <a href='https://codepen.io/azuremaps'>@azuremaps</a> () no <a href='https://codepen.io'>CodePen</a>.
</iframe>

## <a name="clustering-and-the-heat-maps-layer"></a>Agrupamento e camada de mapas de calor

Os mapas de calor são uma ótima maneira de mostrar a densidade de dados no mapa. Este método de visualização pode lidar com um grande número de pontos de dados por si só. Se os pontos de dados estiverem agrupados e o tamanho do cluster for usado como o peso do mapa de calor, então o mapa de calor pode lidar ainda mais com os dados. Para obter esta opção, deite `weight` a opção da camada de mapa de calor para `['get', 'point_count']` . Quando o raio do cluster é pequeno, o mapa de calor será quase idêntico a um mapa de calor usando os pontos de dados não aglomerados, mas funcionará muito melhor. No entanto, quanto menor for o raio do cluster, mais preciso será o mapa de calor, mas com menos benefícios de desempenho.

<br/>

<iframe height="500" style="width: 100%;" scrolling="no" title="Mapa de calor ponderado do cluster" src="//codepen.io/azuremaps/embed/VRJrgO/?height=500&theme-id=0&default-tab=js,result&editable=true" frameborder="no" allowtransparency="true" allowfullscreen="true">
Consulte o <a href='https://codepen.io/azuremaps/pen/VRJrgO/'>Mapa de Calor ponderado</a> do Aglomerado de Canetas por Azure Maps <a href='https://codepen.io/azuremaps'>@azuremaps</a> () no <a href='https://codepen.io'>CodePen</a>.
</iframe>

## <a name="mouse-events-on-clustered-data-points"></a>Eventos de rato em pontos de dados agrupados

Quando os eventos do rato ocorrem numa camada que contém pontos de dados agrupados, o ponto de dados agrupado volta ao evento como um objeto de característica de ponto GeoJSON. Esta função de ponto terá as seguintes propriedades:

| Nome da propriedade             | Tipo    | Descrição   |
|---------------------------|---------|---------------|
| `cluster`                 | boolean | Indica se a característica representa um cluster. |
| `cluster_id`              | cadeia  | Um ID único para o cluster que pode ser usado com o `getClusterExpansionZoom` DataSource, `getClusterChildren` e `getClusterLeaves` métodos. |
| `point_count`             | número  | O número de pontos que o cluster contém.  |
| `point_count_abbreviated` | cadeia  | Uma corda que abrevia o `point_count` valor se for longa. (por exemplo, 4.000 torna-se 4K)  |

Este exemplo toma uma camada de bolha que torna os pontos de cluster e adiciona um evento de clique. Quando o evento de clique dispara, o código calcula e amplia o mapa para o nível de zoom seguinte, no qual o cluster se parte. Esta funcionalidade é implementada usando o `getClusterExpansionZoom` método da classe e a propriedade do ponto de `DataSource` `cluster_id` dados agrupado clicado.

<br/>

<iframe height="500" style="width: 100%;" scrolling="no" title="Cluster getClusterExpansionZoom" src="//codepen.io/azuremaps/embed/moZWeV/?height=500&theme-id=0&default-tab=js,result&editable=true" frameborder="no" allowtransparency="true" allowfullscreen="true">
Consulte o Cluster Pen <a href='https://codepen.io/azuremaps/pen/moZWeV/'>getClusterExpansionZoom</a> by Azure Maps <a href='https://codepen.io/azuremaps'>@azuremaps</a> () em <a href='https://codepen.io'>CodePen</a>.
</iframe>

## <a name="display-cluster-area"></a>Área de aglomerado de exibição 

Os dados de pontos que um cluster representa estão espalhados por uma área. Nesta amostra quando o rato está sobre um aglomerado, ocorrem dois comportamentos principais. Primeiro, os pontos de dados individuais contidos no cluster serão utilizados para calcular um casco convexo. Em seguida, o casco convexo será exibido no mapa para mostrar uma área.  Um casco convexo é um polígono que envolve um conjunto de pontos como uma banda elástica e pode ser calculado usando o `atlas.math.getConvexHull` método. Todos os pontos contidos num cluster podem ser obtidos a partir da fonte de dados utilizando o `getClusterLeaves` método.

<br/>

 <iframe height="500" style="width: 100%;" scrolling="no" title="Casco convexo de área de cluster" src="//codepen.io/azuremaps/embed/QoXqWJ/?height=500&theme-id=0&default-tab=js,result&editable=true" frameborder="no" allowtransparency="true" allowfullscreen="true">
Consulte o <a href='https://codepen.io/azuremaps/pen/QoXqWJ/'>casco convexo da área do Aglomerado de</a> Canetas por Azure Maps <a href='https://codepen.io/azuremaps'>@azuremaps</a> () no <a href='https://codepen.io'>CodePen</a>.
</iframe>

## <a name="aggregating-data-in-clusters"></a>Agregação de dados em clusters

Muitas vezes os aglomerados são representados usando um símbolo com o número de pontos que estão dentro do cluster. Mas, às vezes, é desejável personalizar o estilo de aglomerados com métricas adicionais. Com agregados de cluster, propriedades personalizadas podem ser criadas e povoadas usando um cálculo [de expressão agregado.](data-driven-style-expressions-web-sdk.md#aggregate-expression)  Os agregados de cluster podem ser definidos em `clusterProperties` opção do `DataSource` .

A amostra a seguir utiliza uma expressão agregada. O código calcula uma contagem com base na propriedade tipo entidade de cada ponto de dados num cluster. Quando um utilizador clica num cluster, um popup mostra com informações adicionais sobre o cluster.

<iframe height="500" style="width: 100%;" scrolling="no" title="Agregados de cluster" src="//codepen.io/azuremaps/embed/jgYyRL/?height=500&theme-id=0&default-tab=js,result&editable=true" frameborder="no" allowtransparency="true" allowfullscreen="true">
Consulte os <a href='https://codepen.io/azuremaps/pen/jgYyRL/'>agregados</a> pen cluster por Azure Maps <a href='https://codepen.io/azuremaps'>@azuremaps</a> () no <a href='https://codepen.io'>CodePen</a>.
</iframe>

## <a name="next-steps"></a>Passos seguintes

Saiba mais sobre as aulas e métodos utilizados neste artigo:

> [!div class="nextstepaction"]
> [DataSource classe](/javascript/api/azure-maps-control/atlas.source.datasource)

> [!div class="nextstepaction"]
> [Objeto DataSourceOptions](/javascript/api/azure-maps-control/atlas.datasourceoptions)

> [!div class="nextstepaction"]
> [atlas.math namespace](/javascript/api/azure-maps-control/atlas.math)

Consulte exemplos de código para adicionar funcionalidade à sua aplicação:

> [!div class="nextstepaction"]
> [Adicionar uma camada de bolha](map-add-bubble-layer.md)

> [!div class="nextstepaction"]
> [Adicionar uma camada de símbolo](map-add-pin.md)

> [!div class="nextstepaction"]
> [Adicionar uma camada de mapa térmico](map-add-heat-map-layer.md)