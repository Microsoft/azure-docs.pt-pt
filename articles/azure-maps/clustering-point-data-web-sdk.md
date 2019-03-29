---
title: Clustering de dados de ponto no Azure Maps | Documentos da Microsoft
description: Como criar um cluster de ponto de dados no Web SDK
author: rbrundritt
ms.author: richbrun
ms.date: 03/27/2019
ms.topic: conceptual
ms.service: azure-maps
services: azure-maps
manager: cpendleton
ms.custom: codepen
ms.openlocfilehash: d4dc6f0c8fd2dff74a1997c9dca5a31abc70c03a
ms.sourcegitcommit: c63fe69fd624752d04661f56d52ad9d8693e9d56
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/28/2019
ms.locfileid: "58580827"
---
# <a name="clustering-point-data"></a>Clustering de ponto de dados

Ao visualizar o número de pontos de dados no mapa, pontos se sobrepõem entre si, o mapa parece amontoado e torna-se difícil ver e utilizar. Clustering de ponto de dados pode ser utilizado para melhorar esta experiência de utilizador. Clustering de dados de ponto é o processo de combinar dados de ponto que estejam perto uns dos outros e que representa-los no mapa como um ponto único de dados em cluster. Conforme o usuário amplia para o mapa, os clusters de dividir em seus pontos de dados individuais.

## <a name="enabling-clustering-on-a-data-source"></a>Ativar o clustering numa origem de dados

Clustering facilmente pode ser ativado no `DataSource` classe, definindo o `cluster` opção como true. Além disso, o raio de pixel para selecionar pontos próximos combinar num cluster pode ser definido utilizando o `clusterRadius` e um nível de zoom pode ser especificado no qual pretende desativar o clustering lógica usando o `clusterMaxZoom` opção. Eis um exemplo de como ativar o clustering numa origem de dados.

```javascript
//Create a data source and enable clustering.
var datasource = new atlas.source.DataSource(null, {
    //Tell the data source to cluster point data.
    cluster: true,

    //The radius in pixels to cluster points together.
    clusterRadius: 45,

    //The maximium zoom level in which clustering occurs.
    //If you zoom in more than this, all points are rendered as symbols.
    clusterMaxZoom: 15 
});
```

> [!TIP]
> Se dois pontos de dados próximos no chão, é possível que o cluster será nunca dividir, não importa o quão próximo zooms de utilizador no. Para resolver isso, pode definir o `clusterMaxZoom` opção da origem de dados que especifica a nível de zoom para desativar a lógica de clustering e simplesmente exibir tudo.

O `DataSource` classe também tem os seguintes métodos relacionados com o clustering:

| Método | Tipo de retorno | Descrição |
|--------|-------------|-------------|
| getClusterChildren(clusterId: number) | Promessa&lt;funcionalidade&lt;Geometry, qualquer&gt; \| forma&gt; | Obtém os filhos do cluster especificado no próximo nível de zoom. Esses filhos podem ser uma combinação de formas e subclusters. Os subclusters será funcionalidades com propriedades correspondentes ClusteredProperties. |
| getClusterExpansionZoom(clusterId: number) | Promise&lt;number&gt; | Calcula um nível de zoom em que o cluster irá iniciar a expansão ou dividir. |
| getClusterLeaves(clusterId: number, limit: number, offset: number) | Promessa&lt;funcionalidade&lt;Geometry, qualquer&gt; \| forma&gt; | Obtém todos os pontos de um cluster. Definir o `limit` devolve um subconjunto dos pontos e utilizar o `offset` à página através dos pontos. |

## <a name="display-clusters-using-a-bubble-layer"></a>Clusters de exibição usando uma camada de bolhas

Uma camada de bolha é uma ótima maneira de compor pontos em cluster, à medida que pode facilmente aumentar o radius e alterar a cor-los com base no número de pontos no cluster com uma expressão. Quando visualizar clusters com uma camada de bolhas, também deve utilizar uma camada separada para o processamento de pontos de dados sem cluster. Muitas vezes, é bom para também ser capaz de exibir o tamanho do cluster por cima de bolhas. Uma camada de símbolo com texto e nenhum ícone pode ser usada para atingir esse comportamento. 

<br/>

<iframe height="500" style="width: 100%;" scrolling="no" title="Camada de bolhas básica clustering" src="//codepen.io/azuremaps/embed/qvzRZY/?height=500&theme-id=0&default-tab=js,result" frameborder="no" allowtransparency="true" allowfullscreen="true">
Consulte a caneta <a href='https://codepen.io/azuremaps/pen/qvzRZY/'>clustering da camada de bolhas básica</a> ao Azure Maps (<a href='https://codepen.io/azuremaps'>@azuremaps</a>) na <a href='https://codepen.io'>CodePen</a>.
</iframe>

## <a name="display-clusters-using-a-symbol-layer"></a>Clusters de exibição usando uma camada de símbolo

Quando visualizar os dados de ponto de utilizar a camada de símbolo, por padrão, que ela ocultará automaticamente símbolos essa sobreposição entre si para criar uma experiência mais limpa, no entanto talvez isso não seja a experiência pretendida se quiser ver a densidade de dados de pontos no mapa. Definindo a `allowOverlap` opção das camadas símbolo `iconOptions` propriedade para `true` desativa esta experiência, mas irá resultar em todos os símbolos sendo exibidos. Utilizar o clustering permite-lhe ver a densidade de todos os dados ao criar uma experiência de usuário limpa interessante. Neste exemplo, símbolos personalizados serão utilizados para representar a clusters e pontos de dados individuais.

<br/>

<iframe height="500" style="width: 100%;" scrolling="no" title="Camada de símbolo em cluster" src="//codepen.io/azuremaps/embed/Wmqpzz/?height=500&theme-id=0&default-tab=js,result" frameborder="no" allowtransparency="true" allowfullscreen="true">
Consulte a caneta <a href='https://codepen.io/azuremaps/pen/Wmqpzz/'>camada do símbolo em cluster</a> ao Azure Maps (<a href='https://codepen.io/azuremaps'>@azuremaps</a>) na <a href='https://codepen.io'>CodePen</a>.
</iframe>

## <a name="clustering-and-the-heat-maps-layer"></a>O gráfico de clustering e mapeia a camada

Mapas gráficos são uma excelente maneira de exibir a densidade de dados no mapa. Esta visualização pode processar um grande número de pontos de dados por conta própria, mas pode manipular dados ainda mais se os pontos de dados estão em cluster e o tamanho do cluster é utilizado como o peso do mapa térmico. Definir o `weight` opção da camada de mapa térmico para `['get', 'point_count']` para atingir esse objetivo. Quando o radius de cluster for pequeno, o mapa gráfico será a aparência de quase idêntico de um mapa térmico com os pontos de dados sem cluster, mas será um desempenho muito melhor. No entanto, menor será o radius de cluster, o mais precisa o mapa gráfico irá ser mas com menos de um desempenho se beneficiar.

<br/>

<iframe height="500" style="width: 100%;" scrolling="no" title="Mapa térmico de ponderada de cluster" src="//codepen.io/azuremaps/embed/VRJrgO/?height=500&theme-id=0&default-tab=js,result" frameborder="no" allowtransparency="true" allowfullscreen="true">
Consulte a caneta <a href='https://codepen.io/azuremaps/pen/VRJrgO/'>Cluster ponderada mapa térmico</a> ao Azure Maps (<a href='https://codepen.io/azuremaps'>@azuremaps</a>) na <a href='https://codepen.io'>CodePen</a>.
</iframe>

## <a name="mouse-events-on-clustered-data-points"></a>Eventos de Mouse nos pontos de dados em cluster

Quando ocorrem eventos de mouse numa camada de conter pontos de dados em cluster, o ponto de dados em cluster será retornado para o evento como um objeto de funcionalidade de ponto de GeoJSON. Esta funcionalidade de ponto terá as seguintes propriedades:

| Nome da propriedade | Type | Descrição |
|---------------|------|-------------|
| cluster | boolean | Indica se o recurso representa um cluster. |
| cluster_id | string | Um ID exclusivo para o cluster que pode ser utilizado com a origem de dados `getClusterExpansionZoom`, `getClusterChildren`, e `getClusterLeaves` métodos. |
| point_count | número | O número de pontos que contém o cluster. |
| point_count_abbreviated | string | Uma cadeia de caracteres que abbreviates o valor de point_count se é longa. (por exemplo, 4.000 torna-se 4K) |

Este exemplo usa uma camada de bolhas que processa os pontos de cluster e adiciona um evento de clique que quando acionado, calcular e ampliar o mapa para o próximo nível de zoom em que o cluster irá interromper a diferença com o `getClusterExpansionZoom` método da `DataSource` classe e o `cluster_id` propriedade o clicado em cluster ponto de dados. 

<br/>

<iframe height="500" style="width: 100%;" scrolling="no" title="GetClusterExpansionZoom de cluster" src="//codepen.io/azuremaps/embed/moZWeV/?height=500&theme-id=0&default-tab=js,result" frameborder="no" allowtransparency="true" allowfullscreen="true">
Consulte a caneta <a href='https://codepen.io/azuremaps/pen/moZWeV/'>Cluster getClusterExpansionZoom</a> ao Azure Maps (<a href='https://codepen.io/azuremaps'>@azuremaps</a>) na <a href='https://codepen.io'>CodePen</a>.
</iframe>

## <a name="display-cluster-area"></a>Apresentar a área de cluster 

Os dados de ponto que representa um cluster estão distribuídos por uma área. Neste exemplo, quando o mouse é passado sobre um cluster, os dados serão utilizados para calcular um casco convexo e apresentados no mapa para mostrar a área de pontos que ele contém (folhas). Todos os pontos de contidos num cluster podem ser obtidos da origem de dados com o `getClusterLeaves` método. Um casco convexo é um polígono que encapsula um conjunto de pontos, como uma banda elástico e pode ser calculado utilizando a `atlas.math.getConvexHull` método.

<br/>

 <iframe height="500" style="width: 100%;" scrolling="no" title="Casco convexo de área de cluster" src="//codepen.io/azuremaps/embed/QoXqWJ/?height=500&theme-id=0&default-tab=js,result" frameborder="no" allowtransparency="true" allowfullscreen="true">
Consulte a caneta <a href='https://codepen.io/azuremaps/pen/QoXqWJ/'>casco convexo de área de Cluster</a> ao Azure Maps (<a href='https://codepen.io/azuremaps'>@azuremaps</a>) na <a href='https://codepen.io'>CodePen</a>.
</iframe>

## <a name="next-steps"></a>Passos Seguintes

Saiba mais sobre as classes e métodos usados neste artigo:

> [!div class="nextstepaction"]
> [Classe de origem de dados](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.source.datasource?view=azure-iot-typescript-latest)

> [!div class="nextstepaction"]
> [Objeto de DataSourceOptions](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.datasourceoptions?view=azure-iot-typescript-latest)

> [!div class="nextstepaction"]
> [atlas.math namespace](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.math?view=azure-iot-typescript-latest)

Veja exemplos de código para adicionar funcionalidades à sua aplicação:

> [!div class="nextstepaction"]
> [Adicionar uma camada de bolhas](map-add-bubble-layer.md)

> [!div class="nextstepaction"]
> [Adicionar uma camada de símbolo](map-add-pin.md)

> [!div class="nextstepaction"]
> [Adicionar uma camada de mapa térmico](map-add-heat-map-layer.md)
