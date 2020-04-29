---
title: Dados do ponto de agrupamento num mapa Microsoft Azure Maps
description: Neste artigo, você vai aprender a cluster -point dados e torná-lo em um mapa usando o Microsoft Azure Maps Web SDK.
author: rbrundritt
ms.author: richbrun
ms.date: 07/29/2019
ms.topic: conceptual
ms.service: azure-maps
services: azure-maps
manager: cpendle
ms.custom: codepen
ms.openlocfilehash: ce2891201331ee1efd861d2f13cec78c0551b6ba
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/28/2020
ms.locfileid: "80804576"
---
# <a name="clustering-point-data"></a>Dados de pontos de agrupamento

Ao visualizar muitos pontos de dados no mapa, os pontos de dados podem sobrepor-se uns aos outros. A sobreposição pode causar a ilegível e difícil utilização do mapa. Os dados dos pontos de agrupamento são o processo de combinar dados de pontos que se aproximam uns dos outros e representá-los no mapa como um único ponto de dados agrupado. À medida que o utilizador faz zoom no mapa, os clusters separam-se nos seus pontos de dados individuais. Quando trabalhar com um grande número de pontos de dados, utilize os processos de agrupamento para melhorar a sua experiência de utilizador.

<br/>

<iframe src="https://channel9.msdn.com/Shows/Internet-of-Things-Show/Clustering-point-data-in-Azure-Maps/player" width="960" height="540" allowFullScreen frameBorder="0"></iframe>

## <a name="enabling-clustering-on-a-data-source"></a>Habilitar o agrupamento numa fonte de dados

Ativar o `DataSource` agrupamento na classe, definindo a opção `cluster` como verdadeira. Coloque `ClusterRadius` para selecionar pontos próximos e combine-os em um cluster. O valor `ClusterRadius` está nos pixels. Utilize `clusterMaxZoom` para especificar um nível de zoom para desativar a lógica de agrupamento. Aqui está um exemplo de como permitir o agrupamento numa fonte de dados.

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
> Se dois pontos de dados estiverem próximos um do outro no terreno, é possível que o cluster nunca se desfaça, por mais perto que o utilizador faça zooms. Para resolver isto, pode `clusterMaxZoom` definir a opção de desativar a lógica de agrupamento e simplesmente mostrar tudo.

Aqui estão métodos adicionais que a `DataSource` classe prevê para o agrupamento:

| Método | Tipo de devolução | Descrição |
|--------|-------------|-------------|
| getClusterChildren (clusterId: número) | Promessa&lt;&lt;Array&lt;Feature Geometry, qualquer&gt; \| forma&gt;&gt; | Recupera as crianças do aglomerado dado no próximo nível de zoom. Estas crianças podem ser uma combinação de formas e subaglomerados. Os subclusters serão funcionalidades com propriedades correspondentes ao ClusteredProperties. |
| getClusterExpansionZoom (clusterId: número) | Número&lt;de promessa&gt; | Calcula um nível de zoom no qual o cluster começará a expandir-se ou a separar-se. |
| getClusterLeaves (clusterId: número, limite: número, compensação: número) | Promessa&lt;&lt;Array&lt;Feature Geometry, qualquer&gt; \| forma&gt;&gt; | Recupera todos os pontos num aglomerado. Delineie o `limit` reconjunto para devolver `offset` um subconjunto dos pontos e utilize o para página através dos pontos. |

## <a name="display-clusters-using-a-bubble-layer"></a>Exibir aglomerados usando uma camada de bolha

Uma camada de bolha é uma ótima maneira de renderizar pontos agrupados. Utilize expressões para escalar o raio e altere a cor com base no número de pontos no cluster. Se apresentar aglomerados utilizando uma camada de bolha, então deve utilizar uma camada separada para renderpontos de dados não agrupados.

Para mostrar o tamanho do cluster em cima da bolha, use uma camada de símbolo com texto e não utilize um ícone.

<br/>

<iframe height="500" style="width: 100%;" scrolling="no" title="Agrupamento básico de camada de bolha" src="//codepen.io/azuremaps/embed/qvzRZY/?height=500&theme-id=0&default-tab=js,result&editable=true" frameborder="no" allowtransparency="true" allowfullscreen="true">
Veja o agrupamento de <a href='https://codepen.io/azuremaps/pen/qvzRZY/'>camadas</a> de<a href='https://codepen.io/azuremaps'>@azuremaps</a>bolha básica pen por Azure Maps () no <a href='https://codepen.io'>CodePen</a>.
</iframe>

## <a name="display-clusters-using-a-symbol-layer"></a>Exibir aglomerados usando uma camada de símbolo

Ao visualizar pontos de dados, a camada de símbolo esconde automaticamente símbolos que se sobrepõem mutuamente para garantir uma interface de utilizador mais limpa. Este comportamento padrão pode ser indesejável se quiser mostrar a densidade de pontos de dados no mapa. No entanto, estas definições podem ser alteradas. Para exibir todos os `allowOverlap` símbolos, detete a opção da propriedade das camadas `iconOptions` de símbolo para `true`. 

Utilize o agrupamento para mostrar a densidade dos pontos de dados, mantendo uma interface de utilizador limpa. A amostra abaixo mostra-lhe como adicionar símbolos personalizados e representar clusters e pontos de dados individuais usando a camada de símbolo.

<br/>

<iframe height="500" style="width: 100%;" scrolling="no" title="Camada de símbolo agrupada" src="//codepen.io/azuremaps/embed/Wmqpzz/?height=500&theme-id=0&default-tab=js,result&editable=true" frameborder="no" allowtransparency="true" allowfullscreen="true">
Consulte a camada de <a href='https://codepen.io/azuremaps/pen/Wmqpzz/'>símbolo agrupada</a> da caneta por Azure Maps ()<a href='https://codepen.io/azuremaps'>@azuremaps</a>em <a href='https://codepen.io'>CodePen</a>.
</iframe>

## <a name="clustering-and-the-heat-maps-layer"></a>Agrupamento e a camada de mapas de calor

Os mapas de calor são uma ótima maneira de mostrar a densidade de dados no mapa. Este método de visualização pode lidar com um grande número de pontos de dados por si só. Se os pontos de dados forem agrupados e o tamanho do cluster for usado como o peso do mapa de calor, então o mapa de calor pode lidar com ainda mais dados. Para obter esta opção, detete a opção `weight` da camada do mapa de calor para `['get', 'point_count']`. Quando o raio de aglomerado é pequeno, o mapa de calor será quase idêntico a um mapa de calor usando os pontos de dados não agrupados, mas irá ter um desempenho muito melhor. No entanto, quanto menor for o raio de cluster, mais preciso será o mapa de calor, mas com menos benefícios de desempenho.

<br/>

<iframe height="500" style="width: 100%;" scrolling="no" title="Mapa de calor ponderado do cluster" src="//codepen.io/azuremaps/embed/VRJrgO/?height=500&theme-id=0&default-tab=js,result&editable=true" frameborder="no" allowtransparency="true" allowfullscreen="true">
Consulte o Mapa de <a href='https://codepen.io/azuremaps/pen/VRJrgO/'>Calor ponderado</a> pelo<a href='https://codepen.io/azuremaps'>@azuremaps</a>Azure Maps () no <a href='https://codepen.io'>CodePen</a>.
</iframe>

## <a name="mouse-events-on-clustered-data-points"></a>Eventos de rato em pontos de dados agrupados

Quando os eventos do rato ocorrem numa camada que contém pontos de dados agrupados, o ponto de dados agrupado retorna ao evento como um objeto de característica de ponto GeoJSON. Esta funcionalidade de ponto terá as seguintes propriedades:

| Nome da propriedade             | Tipo    | Descrição   |
|---------------------------|---------|---------------|
| `cluster`                 | boolean | Indica se a funcionalidade representa um cluster. |
| `cluster_id`              | string  | Um ID único para o cluster que `getClusterExpansionZoom`pode `getClusterChildren`ser `getClusterLeaves` usado com o DataSource, e métodos. |
| `point_count`             | número  | O número de pontos que o cluster contém.  |
| `point_count_abbreviated` | string  | Uma corda que abreodia o `point_count` valor se for longo. (por exemplo, 4.000 torna-se 4K)  |

Este exemplo pega numa camada de bolha que torna os pontos de cluster e adiciona um evento de clique. Quando o clique dispara, o código calcula e amplia o mapa para o próximo nível de zoom, no qual o cluster se desfaz. Esta funcionalidade é implementada `getClusterExpansionZoom` utilizando o `DataSource` método `cluster_id` da classe e a propriedade do ponto de dados agrupado clicado.

<br/>

<iframe height="500" style="width: 100%;" scrolling="no" title="Cluster getClusterExpansionZoom" src="//codepen.io/azuremaps/embed/moZWeV/?height=500&theme-id=0&default-tab=js,result&editable=true" frameborder="no" allowtransparency="true" allowfullscreen="true">
Veja o Cluster Pen <a href='https://codepen.io/azuremaps/pen/moZWeV/'>getClusterExpansionZoom</a> <a href='https://codepen.io/azuremaps'>@azuremaps</a>by Azure Maps () em <a href='https://codepen.io'>CodePen</a>.
</iframe>

## <a name="display-cluster-area"></a>Área de aglomerado de exibição 

Os dados de pontos que um cluster representa estão espalhados por uma área. Nesta amostra quando o rato é pairado sobre um aglomerado, ocorrem dois comportamentos principais. Em primeiro lugar, os pontos de dados individuais contidos no cluster serão utilizados para calcular um casco convexo. Em seguida, o casco convexo será exibido no mapa para mostrar uma área.  Um casco convexo é um polígono que envolve um conjunto de `atlas.math.getConvexHull` pontos como uma banda elástica e pode ser calculado usando o método. Todos os pontos contidos num cluster podem ser `getClusterLeaves` recuperados a partir da fonte de dados utilizando o método.

<br/>

 <iframe height="500" style="width: 100%;" scrolling="no" title="Casco convexo de área de aglomerado" src="//codepen.io/azuremaps/embed/QoXqWJ/?height=500&theme-id=0&default-tab=js,result&editable=true" frameborder="no" allowtransparency="true" allowfullscreen="true">
Veja o <a href='https://codepen.io/azuremaps/pen/QoXqWJ/'>casco convexa</a> da área<a href='https://codepen.io/azuremaps'>@azuremaps</a>do Cluster Pen por Azure Maps () em <a href='https://codepen.io'>CodePen</a>.
</iframe>

## <a name="aggregating-data-in-clusters"></a>Agregação de dados em clusters

Muitas vezes os clusters são representados usando um símbolo com o número de pontos que estão dentro do cluster. Mas, às vezes, é desejável personalizar o estilo dos clusters com métricas adicionais. Com agregados de cluster, as propriedades personalizadas podem ser criadas e povoadas usando um cálculo de [expressão agregado.](data-driven-style-expressions-web-sdk.md#aggregate-expression)  Os agregados de `clusterProperties` cluster podem `DataSource`ser definidos na opção do .

A amostra seguinte utiliza uma expressão agregada. O código calcula uma contagem com base na propriedade do tipo entidade de cada ponto de dados num cluster. Quando um utilizador clica num cluster, um popup mostra com informações adicionais sobre o cluster.

<iframe height="500" style="width: 100%;" scrolling="no" title="Agregados de cluster" src="//codepen.io/azuremaps/embed/jgYyRL/?height=500&theme-id=0&default-tab=js,result&editable=true" frameborder="no" allowtransparency="true" allowfullscreen="true">
Veja os <a href='https://codepen.io/azuremaps/pen/jgYyRL/'>agregados</a> do Cluster<a href='https://codepen.io/azuremaps'>@azuremaps</a>pen por Azure Maps () em <a href='https://codepen.io'>CodePen</a>.
</iframe>

## <a name="next-steps"></a>Passos seguintes

Saiba mais sobre as aulas e métodos utilizados neste artigo:

> [!div class="nextstepaction"]
> [Classe DataSource](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.source.datasource?view=azure-iot-typescript-latest)

> [!div class="nextstepaction"]
> [Objeto DataSourceOptions](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.datasourceoptions?view=azure-iot-typescript-latest)

> [!div class="nextstepaction"]
> [atlas.math namespace](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.math?view=azure-iot-typescript-latest)

Consulte exemplos de código para adicionar funcionalidade à sua aplicação:

> [!div class="nextstepaction"]
> [Adicionar uma camada de bolha](map-add-bubble-layer.md)

> [!div class="nextstepaction"]
> [Adicionar uma camada de símbolo](map-add-pin.md)

> [!div class="nextstepaction"]
> [Adicionar uma camada de mapa térmico](map-add-heat-map-layer.md)
