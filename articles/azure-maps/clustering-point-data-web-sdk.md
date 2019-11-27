---
title: Dados de ponto de clustering no Azure Maps | Microsoft Docs
description: Como agrupar dados de ponto de cluster no SDK da Web
author: rbrundritt
ms.author: richbrun
ms.date: 07/29/2019
ms.topic: conceptual
ms.service: azure-maps
services: azure-maps
manager: cpendleton
ms.custom: codepen
ms.openlocfilehash: 4a583f77aac036028fd75d3c05af805031f08ebd
ms.sourcegitcommit: 8cf199fbb3d7f36478a54700740eb2e9edb823e8
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 11/25/2019
ms.locfileid: "74480552"
---
# <a name="clustering-point-data"></a>Dados do ponto de cluster

Ao Visualizar muitos pontos de dados no mapa, os pontos se sobrepõem, o mapa fica confuso e torna-se difícil ver e usar. O clustering de dados de ponto pode ser usado para melhorar a experiência do usuário. Os dados do ponto de cluster são o processo de combinação de dados de ponto que estão próximos uns dos outros e os representa no mapa como um único ponto de dados clusterizado. À medida que o usuário se aplica ao mapa, os clusters se dividem em seus pontos de dados individuais.

<br/>

<iframe src="https://channel9.msdn.com/Shows/Internet-of-Things-Show/Clustering-point-data-in-Azure-Maps/player" width="960" height="540" allowFullScreen frameBorder="0"></iframe>

## <a name="enabling-clustering-on-a-data-source"></a>Habilitando o clustering em uma fonte de dados

O clustering pode ser facilmente habilitado na classe `DataSource` definindo a opção `cluster` como true. Além disso, o raio de pixel para selecionar pontos próximos a serem combinados em um cluster pode ser definido usando o `clusterRadius` e um nível de zoom pode ser especificado para desabilitar a lógica de clustering usando a opção `clusterMaxZoom`. Aqui está um exemplo de como habilitar o clustering em uma fonte de dados.

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
> Se dois pontos de dados estiverem próximos juntos no chão, é possível que o cluster nunca se divida, independentemente de como fechar o usuário se amplia. Para resolver isso, você pode definir a opção `clusterMaxZoom` da fonte de dados que especifica no nível de zoom para desabilitar a lógica de clustering e simplesmente exibir tudo.

A classe `DataSource` também tem os seguintes métodos relacionados ao clustering:

| Método | Tipo de retorno | Descrição |
|--------|-------------|-------------|
| getClusterChildren(clusterId: number) | O recurso de&lt;de matriz de promessa&lt;&lt;geometria, qualquer&gt; \|&gt;de forma &gt; | Recupera os filhos do cluster fornecido no próximo nível de zoom. Esses filhos podem ser uma combinação de formas e subclusters. Os subclusters serão recursos com propriedades correspondentes a ClusteredProperties. |
| getClusterExpansionZoom(clusterId: number) | Número de&lt;de promessa&gt; | Calcula um nível de zoom no qual o cluster começará a se expandir ou separar. |
| getClusterLeaves(clusterId: number, limit: number, offset: number) | O recurso de&lt;de matriz de promessa&lt;&lt;geometria, qualquer&gt; \|&gt;de forma &gt; | Recupera todos os pontos em um cluster. Defina o `limit` para retornar um subconjunto dos pontos e use a `offset` para paginar os pontos. |

## <a name="display-clusters-using-a-bubble-layer"></a>Exibir clusters usando uma camada de bolha

Uma camada de bolha é uma ótima maneira de renderizar pontos clusterizados, pois você pode dimensionar facilmente o raio e alterar a cor deles com base no número de pontos no cluster usando uma expressão. Ao exibir clusters usando uma camada de bolha, você também deve usar uma camada separada para renderizar pontos de dados não clusterizados. Geralmente, é bom também poder exibir o tamanho do cluster sobre as bolhas. Uma camada de símbolo com texto e nenhum ícone pode ser usado para alcançar esse comportamento. 

<br/>

<iframe height="500" style="width: 100%;" scrolling="no" title="Clustering de camada de bolha básica" src="//codepen.io/azuremaps/embed/qvzRZY/?height=500&theme-id=0&default-tab=js,result" frameborder="no" allowtransparency="true" allowfullscreen="true">
Consulte o <a href='https://codepen.io/azuremaps/pen/qvzRZY/'>agrupamento da camada de bolha básica</a> da caneta pelo Azure Maps (<a href='https://codepen.io/azuremaps'>@azuremaps</a>) em <a href='https://codepen.io'>CodePen</a>.
</iframe>

## <a name="display-clusters-using-a-symbol-layer"></a>Exibir clusters usando uma camada de símbolo

Ao visualizar os dados de ponto usando a camada de símbolo, por padrão, ele ocultará automaticamente os símbolos que se sobrepõem para criar uma experiência de limpeza. no entanto, essa pode não ser a experiência desejada se você quiser ver a densidade dos pontos de dados no mapa. Definir a opção `allowOverlap` da propriedade `iconOptions` camadas de símbolo como `true` desabilita essa experiência, mas resultará na exibição de todos os símbolos. O uso de clustering permite que você veja a densidade de todos os dados ao criar uma boa experiência de usuário clara. Neste exemplo, os símbolos personalizados serão usados para representar clusters e pontos de dados individuais.

<br/>

<iframe height="500" style="width: 100%;" scrolling="no" title="Camada de símbolo clusterizado" src="//codepen.io/azuremaps/embed/Wmqpzz/?height=500&theme-id=0&default-tab=js,result" frameborder="no" allowtransparency="true" allowfullscreen="true">
Consulte a <a href='https://codepen.io/azuremaps/pen/Wmqpzz/'>camada de símbolo clusterizado</a> por caneta pelo mapas do Azure (<a href='https://codepen.io/azuremaps'>@azuremaps</a>) em <a href='https://codepen.io'>CodePen</a>.
</iframe>

## <a name="clustering-and-the-heat-maps-layer"></a>Clustering e a camada de mapas de calor

Mapas de calor são uma ótima maneira de exibir a densidade dos dados no mapa. Essa visualização pode lidar com um grande número de pontos de dados por conta própria, mas pode manipular ainda mais dados se os pontos de dados são clusterizados e o tamanho do cluster é usado como o peso do mapa de calor. Defina a opção `weight` da camada do mapa de calor como `['get', 'point_count']` para conseguir isso. Quando o raio do cluster for pequeno, o mapa de calor será quase idêntico a um mapa de calor usando os pontos de dados não clusterizados, mas será muito melhor. No entanto, quanto menor for o raio do cluster, mais preciso será o mapa de calor, mas com um benefício de desempenho menor.

<br/>

<iframe height="500" style="width: 100%;" scrolling="no" title="Mapa de calor ponderado do cluster" src="//codepen.io/azuremaps/embed/VRJrgO/?height=500&theme-id=0&default-tab=js,result" frameborder="no" allowtransparency="true" allowfullscreen="true">
Consulte o <a href='https://codepen.io/azuremaps/pen/VRJrgO/'>mapa de calor ponderado do cluster</a> de caneta pelo mapas do Azure (<a href='https://codepen.io/azuremaps'>@azuremaps</a>) em <a href='https://codepen.io'>CodePen</a>.
</iframe>

## <a name="mouse-events-on-clustered-data-points"></a>Eventos de mouse em pontos de dados clusterizados

Quando ocorrem eventos de mouse em uma camada que contém pontos de dados clusterizados, o ponto de dados clusterizado será retornado ao evento como um objeto de recurso de ponto geojson. Esse recurso de ponto terá as seguintes propriedades:

| Nome da propriedade | Tipo | Descrição |
|---------------|------|-------------|
| em | boolean | Indica se o recurso representa um cluster. |
| cluster_id | string | Uma ID exclusiva para o cluster que pode ser usada com os métodos DataSource `getClusterExpansionZoom`, `getClusterChildren`e `getClusterLeaves`. |
| point_count | número | O número de pontos que o cluster contém. |
| point_count_abbreviated | string | Uma cadeia de caracteres que abrevia o valor de `point_count` se for longo. (por exemplo, 4.000 torna-se 4K) |

Este exemplo usa uma camada de bolha que renderiza pontos de cluster e adiciona um evento de clique que, quando disparado, calcula e aplica zoom ao mapa para o próximo nível de zoom no qual o cluster será separado usando o método `getClusterExpansionZoom` da classe `DataSource` e a propriedade `cluster_id` do ponto de dados clusterizado clicado. 

<br/>

<iframe height="500" style="width: 100%;" scrolling="no" title="GetClusterExpansionZoom de cluster" src="//codepen.io/azuremaps/embed/moZWeV/?height=500&theme-id=0&default-tab=js,result" frameborder="no" allowtransparency="true" allowfullscreen="true">
Consulte o <a href='https://codepen.io/azuremaps/pen/moZWeV/'>getClusterExpansionZoom de cluster</a> de caneta pelo mapas do Azure (<a href='https://codepen.io/azuremaps'>@azuremaps</a>) em <a href='https://codepen.io'>CodePen</a>.
</iframe>

## <a name="display-cluster-area"></a>Exibir área do cluster 

Os dados de ponto que um cluster representa são distribuídos por uma área. Neste exemplo, quando o mouse é focalizado em um cluster, os pontos de dados individuais que ele contém (folhas) serão usados para calcular um envoltória de convexa e exibido no mapa para mostrar a área. Todos os pontos contidos em um cluster podem ser recuperados da fonte de dados usando o método `getClusterLeaves`. Um convexa envoltória é um polígono que encapsula um conjunto de pontos como uma faixa elástica e pode ser calculado usando o método `atlas.math.getConvexHull`.

<br/>

 <iframe height="500" style="width: 100%;" scrolling="no" title="Área de cluster convexa envoltória" src="//codepen.io/azuremaps/embed/QoXqWJ/?height=500&theme-id=0&default-tab=js,result" frameborder="no" allowtransparency="true" allowfullscreen="true">
Consulte a área de cluster de caneta <a href='https://codepen.io/azuremaps/pen/QoXqWJ/'>convexa envoltória</a> pelo Azure Maps (<a href='https://codepen.io/azuremaps'>@azuremaps</a>) em <a href='https://codepen.io'>CodePen</a>.
</iframe>

## <a name="aggregating-data-in-clusters"></a>Agregando dados em clusters

Geralmente, os clusters são representados usando um símbolo com o número de pontos que estão dentro do cluster. no entanto, às vezes é desejável personalizar ainda mais o estilo dos clusters com base em alguma métrica, como a receita total de todos os pontos em um cluster. Com agregações de cluster, as propriedades personalizadas podem ser criadas e populadas usando um cálculo de [expressão de agregação](data-driven-style-expressions-web-sdk.md#aggregate-expression) .  As agregações de cluster podem ser definidas na opção `clusterProperties` da `DataSource`.

O exemplo a seguir usa uma expressão de agregação para calcular uma contagem com base na propriedade de tipo de entidade de cada ponto de dados em um cluster.

<iframe height="500" style="width: 100%;" scrolling="no" title="Agregações de cluster" src="//codepen.io/azuremaps/embed/jgYyRL/?height=500&theme-id=0&default-tab=js,result" frameborder="no" allowtransparency="true" allowfullscreen="true">
Consulte o cluster de caneta que <a href='https://codepen.io/azuremaps/pen/jgYyRL/'>agrega</a> pelo Azure Maps (<a href='https://codepen.io/azuremaps'>@azuremaps</a>) em <a href='https://codepen.io'>CodePen</a>.
</iframe>

## <a name="next-steps"></a>Passos seguintes

Saiba mais sobre as classes e os métodos usados neste artigo:

> [!div class="nextstepaction"]
> [Classe DataSource](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.source.datasource?view=azure-iot-typescript-latest)

> [!div class="nextstepaction"]
> [Objeto dataSourceOptions](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.datasourceoptions?view=azure-iot-typescript-latest)

> [!div class="nextstepaction"]
> [namespace do Atlas. Math](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.math?view=azure-iot-typescript-latest)

Consulte exemplos de código para adicionar funcionalidade ao seu aplicativo:

> [!div class="nextstepaction"]
> [Adicionar uma camada de bolha](map-add-bubble-layer.md)

> [!div class="nextstepaction"]
> [Adicionar uma camada de símbolo](map-add-pin.md)

> [!div class="nextstepaction"]
> [Adicionar uma camada do mapa de calor](map-add-heat-map-layer.md)
