---
title: Adicionar uma camada de mapa térmico para o Azure Maps | Documentos da Microsoft
description: Como adicionar uma camada de mapa térmico para o mapa de Javascript
author: rbrundritt
ms.author: richbrun
ms.date: 12/2/2018
ms.topic: conceptual
ms.service: azure-maps
services: azure-maps
manager: ''
ms.custom: codepen
ms.openlocfilehash: 82a6d6b2af7df91696844b09fb7650c547cb6bd1
ms.sourcegitcommit: 62d3a040280e83946d1a9548f352da83ef852085
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/08/2019
ms.locfileid: "59258866"
---
# <a name="add-a-heat-map-layer"></a>Adicionar uma camada de mapa térmico

Mapas de calor, também conhecido como ponto de mapas de densidade, são um tipo de visualização de dados utilizada para representar a densidade dos dados através de um intervalo de cores. Eles são utilizados com frequência para mostrar os dados "pontos de acesso" num mapa e são uma ótima maneira de processar grandes conjuntos de dados do ponto.  Por exemplo, processamento de dezenas de milhares de pontos de dentro da vista do mapa como símbolos, abrange a maior parte da área do mapa e resultaria em muitos símbolos sobreposição eachother, tornando difícil obter mais informações sobre os dados. No entanto, visualizar esse mesmo conjunto de dados como um mapa térmico torna mais fácil ver onde os dados de ponto são o densest e a densidade relativa a outras áreas. Existem muitos cenários em que térmico mapas, são usados. Aqui estão alguns exemplos;

* Dados de temperatura normalmente são processados como mapa térmico, pois fornece aproximações para que a temperatura entre dois pontos de dados.
* Compor os dados de sensores de ruído como um mapa térmico não só mostra a intensidade de ruído em que é o sensor, mas também pode fornecer informações sobre a dissipação através de uma distância. O nível de ruído em qualquer um site pode não ser alto, no entanto, se a área de cobertura de ruído de vários sensores sobrepõe-se, é possível que esta área de sobreposição poderá experienciar níveis mais altos de ruído e, portanto, seria visível no mapa térmico.
* Visualizar um GPS o rastreamento, que inclui a velocidade como um mapa de ponderado altura em que a intensidade de cada ponto de dados baseia-se na velocidade de é uma ótima maneira de ver onde foi acelerando o veículo.

> [!TIP]
> Camadas de mapa de calor por predefinição processará as coordenadas de todas as geometrias numa origem de dados. Para limitar a camada para que ele definido somente composições máximas do ponto de recursos de geometry, a `filter` propriedade da camada para `['==', ['geometry-type'], 'Point']` ou `['any', ['==', ['geometry-type'], 'Point'], ['==', ['geometry-type'], 'MultiPoint']]` se quiser incluem também funcionalidades MultiPoint.

## <a name="add-a-heat-map-layer"></a>Adicionar uma camada de mapa térmico

Para compor uma origem de dados de pontos de como um mapa de calor, passar a sua origem de dados para uma instância do `HeatMapLayer` de classe e adicioná-lo ao mapa como mostrado aqui.

<br/>

<iframe height='500' scrolling='no' title='Camada do mapa térmico simples' src='//codepen.io/azuremaps/embed/gQqdQB/?height=500&theme-id=0&default-tab=js,result&embed-version=2&editable=true' frameborder='no' allowtransparency='true' allowfullscreen='true' style='width: 100%;'>Consulte a caneta <a href='https://codepen.io/azuremaps/pen/gQqdQB/'>camada do mapa térmico simples</a> através do Azure Maps (<a href='https://codepen.io/azuremaps'>@azuremaps</a>) na <a href='https://codepen.io'>CodePen</a>.
</iframe>

Neste exemplo, cada ponto de calor tem um raio de 10 pixels em todos os níveis de zoom. Ao adicionar a camada do mapa térmico ao mapa, este exemplo insere-lo abaixo da camada de etiqueta para criar uma melhor experiência de utilizador, como as etiquetas são claramente visíveis acima do mapa térmico. Os dados neste exemplo são obtidos a partir da [USGS Sismo perigos programa](https://earthquake.usgs.gov/) e representa sismos significativos que ocorreram nos últimos 30 dias.

## <a name="customizing-the-heat-map-layer"></a>Personalizando a camada do mapa térmico

O exemplo anterior personalizada mapa térmico definindo as opções de radius e a opacidade. A camada do mapa térmico fornece várias opções de personalização;

* `radius`: Define um raio de pixel no qual processar cada ponto de dados. Raio pode ser definido como um número fixo ou como uma expressão. Utilização de uma expressão, é possível dimensionar o radius com base no nível de zoom, o que parece representar uma área geográfica consistente no mapa (por exemplo, 5 raio).
* `color`: Especifica a forma como o mapa gráfico é colorido. Uma gradação de cor, muitas vezes, é utilizada para mapas gráficos e pode ser a atingir com um `interpolate` expressão. Usando um `step` expressão para colorir o mapa gráfico divide a densidade visualmente em intervalos que mais se assemelha a um mapa de estilo contour ou radar. Estes paletas de cores, definir as cores do mínimo ao valor máximo de densidade. Valores de cor para mapas gráficos são especificadas como uma expressão no `heatmap-density` valor. A cor no índice 0 numa expressão de interpolação ou a cor predefinida de uma expressão de passo, define a cor da área onde há dados não e pode ser usado para definir uma cor de fundo. Muitos preferem definir este valor como transparente ou um preto semitransparente. Seguem-se exemplos de expressões de cor;

| Expressão da cor interpolação | Expressão da cor gradual | 
|--------------------------------|--------------------------|
| \[<br/>&nbsp;&nbsp;&nbsp;&nbsp;'interpolar',<br/>&nbsp;&nbsp;&nbsp;&nbsp;\['linear'\],<br/>&nbsp;&nbsp;&nbsp;&nbsp;\['heatmap-density'\],<br/>&nbsp;&nbsp;&nbsp;&nbsp;0, 'transparent',<br/>&nbsp;&nbsp;&nbsp;&nbsp;0,01, 'roxa',<br/>&nbsp;&nbsp;&nbsp;&nbsp;0.5, '#fb00fb',<br/>&nbsp;&nbsp;&nbsp;&nbsp;1, '#00c3ff'<br/>\] | \[<br/>&nbsp;&nbsp;&nbsp;&nbsp;'step',<br/>&nbsp;&nbsp;&nbsp;&nbsp;\['heatmap-density'\],<br/>&nbsp;&nbsp;&nbsp;&nbsp;'transparent',<br/>&nbsp;&nbsp;&nbsp;&nbsp;0.01, 'navy',<br/>&nbsp;&nbsp;&nbsp;&nbsp;0.25, 'green',<br/>&nbsp;&nbsp;&nbsp;&nbsp;0.50, 'amarelo',<br/>&nbsp;&nbsp;&nbsp;&nbsp;como 0,75, "vermelho"<br/>\] | 

* `opacity`: Especifica como opaco ou transparente do calor é da camada do mapa.
* `intensity`: Aplica-se um multiplicador para a importância de cada ponto de dados para aumentar a intensidade geral do mapa térmico e ajuda a tornar as pequenas diferenças na importância de se tornar mais fácil de visualizar os pontos de dados.
* `weight`: Por predefinição, todos os pontos de dados tem uma ponderação de 1, portanto, todos os pontos de dados são ponderados igualmente. A opção de peso age como um multiplicador e pode ser definida como um número ou uma expressão. Se um número é definido como o peso, digamos que a 2, seria o equivalente de colocar cada ponto de dados no mapa duas vezes, duplicação, portanto, a densidade. Definir a opção de peso para um número renderiza o mapa térmico de forma semelhante para utilizar a opção de intensidade. No entanto, se for utilizada uma expressão, o peso de cada ponto de dados pode basear-se nas propriedades de cada ponto de dados. Dados de sismo tome como exemplo, cada ponto de dados representam um terramoto. Uma métrica importante cada ponto de dados do sismo tem, é um valor de magnitude. Sismos ocorrem o tempo todo, mas a maioria tem uma magnitude baixa e até mesmo não é sentidas. Ponto será usando o valor de magnitude numa expressão para atribuir o peso para cada dados permite que o mais significativos sismos melhor ser representados no mapa térmico.
* Além das opções de camada de base; Mín/Máx de zoom, visíveis e filtrar, há também uma `source` opção se pretender atualizar a origem de dados e `source-layer` opção se a sua origem de dados é uma origem de mosaico do vetor.

Aqui está uma ferramenta para testar as opções de camada de mapa térmico diferentes.

<br/>

<iframe height='700' scrolling='no' title='Opções de camada do mapa térmico' src='//codepen.io/azuremaps/embed/WYPaXr/?height=700&theme-id=0&default-tab=result' frameborder='no' allowtransparency='true' allowfullscreen='true' style='width: 100%;'>Consulte a caneta <a href='https://codepen.io/azuremaps/pen/WYPaXr/'>opções de camada de mapa de calor</a> através do Azure Maps (<a href='https://codepen.io/azuremaps'>@azuremaps</a>) na <a href='https://codepen.io'>CodePen</a>.
</iframe>

## <a name="consistent-zoomable-heat-map"></a>Mapa térmico zoomable consistente

Por predefinição, o radius de pontos de dados compostos na camada do mapa térmico têm um raio de pixel fixo para todos os níveis de zoom. Como o map é ampliado as agregações de dados em conjunto e a camada do mapa térmico parece diferente. A `zoom` expressão pode ser utilizada para aumentar o radius para cada nível de zoom, de modo a que cada ponto de dados abrange a mesma área física do mapa. Isso fará com que a camada do mapa térmico parecer mais estáticas e consistente. Cada nível de zoom do mapa tem duas vezes mais pixels na vertical e horizontalmente como o nível de zoom anterior. Dimensionar o radius, de modo que ele duplica com cada nível de zoom, irá criar um mapa de calor que parece consistente em todos os níveis de zoom. Isso pode ser feito utilizando o `zoom` com base 2 `exponential interpolation` expressão, conforme mostrado no exemplo abaixo. Amplie o mapa para ver como o mapa gráfico dimensiona com o nível de zoom.

<br/>

<iframe height="500" style="width: 100%;" scrolling="no" title="Mapa térmico zoomable consistente" src="//codepen.io/azuremaps/embed/OGyMZr/?height=500&theme-id=light&default-tab=js,result" frameborder="no" allowtransparency="true" allowfullscreen="true">
Consulte a caneta <a href='https://codepen.io/azuremaps/pen/OGyMZr/'>mapa térmico zoomable consistente</a> ao Azure Maps (<a href='https://codepen.io/azuremaps'>@azuremaps</a>) na <a href='https://codepen.io'>CodePen</a>.
</iframe>

> [!TIP]
> Ao ativar o clustering na origem de dados, os pontos que estão próximos uns dos outros são agrupados em conjunto como um ponto em cluster. A contagem de ponto de cada cluster pode ser utilizado como a expressão de peso para o mapa gráfico e reduzir significativamente o número de pontos que têm de ser de composição. A contagem de ponto de um cluster é armazenada num `point_count` propriedade do recurso de ponto, conforme mostrado abaixo. 
> ```JavaScript
> var layer = new atlas.layer.HeatMapLayer(datasource, null, {
>    weight: ['get', 'point_count']
> });
> ```
> Se o raio de clustering é apenas alguns pixels lá irá ser visual pouca diferença o processamento. Um raio maior serão agrupar mais pontos em cada cluster e melhorar o desempenho do mapa térmico, mas ter o mais notável serão as diferenças.

## <a name="next-steps"></a>Passos Seguintes

Saiba mais sobre as classes e métodos usados neste artigo:

> [!div class="nextstepaction"]
> [HeatMapLayer](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.htmlmarker?view=azure-iot-typescript-latest)

> [!div class="nextstepaction"]
> [HeatMapLayerOptions](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.heatmaplayeroptions?view=azure-iot-typescript-latest)

Para obter mais exemplos de código adicionar a seus mapas, veja os artigos seguintes:

> [!div class="nextstepaction"]
> [Adicionar uma camada de símbolo](./map-add-pin.md)

> [!div class="nextstepaction"]
> [Utilizar expressões de estilo condicionada por dados](data-driven-style-expressions-web-sdk.md)