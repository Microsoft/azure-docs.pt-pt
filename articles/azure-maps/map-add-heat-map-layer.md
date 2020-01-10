---
title: Adicionar uma camada do mapa de calor ao Azure Maps | Microsoft Docs
description: Como adicionar uma camada do mapa de calor ao SDK da Web do Azure Maps.
author: rbrundritt
ms.author: richbrun
ms.date: 07/29/2019
ms.topic: conceptual
ms.service: azure-maps
services: azure-maps
manager: ''
ms.custom: codepen
ms.openlocfilehash: b0d07f18682bf39558180753de38a9c5ff106ee3
ms.sourcegitcommit: f4f626d6e92174086c530ed9bf3ccbe058639081
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 12/25/2019
ms.locfileid: "75408528"
---
# <a name="add-a-heat-map-layer"></a>Adicionar uma camada de mapa térmico

Mapas de calor, também conhecidos como mapas de densidade de ponto, são um tipo de visualização de dados usado para representar a densidade de dados usando um intervalo de cores. Geralmente, eles são usados para mostrar os dados de "pontos de acesso" em um mapa e são uma ótima maneira de renderizar grandes DataSets de ponto. 

Por exemplo, a renderização de dezenas de milhares de pontos na exibição do mapa como símbolos cobre a maior parte da área do mapa. Isso provavelmente resulta em muitos símbolos sobrepostos, tornando difícil obter muito informações sobre os dados. No entanto, Visualizar esse mesmo conjunto de dados como um mapa de calor facilita a visualização de onde os dados do ponto são mais densos e a densidade relativa a outras áreas.

Você pode usar mapas de calor em vários cenários diferentes, incluindo:

- **Dados de temperatura**: fornece aproximações para o que é a temperatura entre dois pontos de dados.
- **Dados para sensores de ruído**: mostra não apenas a intensidade do ruído em que o sensor é, mas também pode fornecer informações sobre a dissipação ao longo de uma distância. O nível de ruído em um site pode não estar alto. Mas se a área de cobertura de ruído de vários sensores se sobrepuser, é possível que essa área sobreposta possa experimentar níveis de ruído maiores e, portanto, estaria visível no mapa de calor.
- **Rastreamento de GPS**: inclui a velocidade como um mapa de altura ponderado, em que a intensidade de cada ponto de dados é baseada na velocidade. Por exemplo, isso fornece uma maneira de ver onde um veículo estava acelerando.

> [!TIP]
> Por padrão, as camadas do mapa de calor renderizam as coordenadas de todas as geometrias em uma fonte de dados. Para limitar a camada para que ela só processe recursos de geometria de ponto, defina a propriedade `filter` da camada como `['==', ['geometry-type'], 'Point']`. Se você também quiser incluir recursos do MultiPoint, defina a propriedade `filter` da camada como `['any', ['==', ['geometry-type'], 'Point'], ['==', ['geometry-type'], 'MultiPoint']]`.

<br/>

<iframe src="https://channel9.msdn.com/Shows/Internet-of-Things-Show/Heat-Maps-and-Image-Overlays-in-Azure-Maps/player" width="960" height="540" allowFullScreen frameBorder="0"></iframe>

## <a name="add-a-heat-map-layer"></a>Adicionar uma camada de mapa térmico

Para renderizar uma fonte de dados de pontos como um mapa de calor, passe sua fonte de dados para uma instância da classe `HeatMapLayer` e adicione-a ao mapa.

No código a seguir, cada ponto de calor tem um raio de 10 pixels em todos os níveis de zoom. Quando você adiciona a camada do mapa de calor ao mapa, este exemplo a insere abaixo da camada de rótulo, para criar uma melhor experiência do usuário. Os rótulos são claramente visíveis acima do mapa de calor. Os dados neste exemplo são originados do programa de [riscos de terremoto USGS](https://earthquake.usgs.gov/)e representam terremotos significativos que ocorreram nos últimos 30 dias.

```javascript
//Create a data source and add it to the map.
var datasource = new atlas.source.DataSource();
map.sources.add(datasource);

//Load a dataset of points, in this case earthquake data from the USGS.
datasource.importDataFromUrl('https://earthquake.usgs.gov/earthquakes/feed/v1.0/summary/all_week.geojson');

//Create a heatmap and add it to the map.
map.layers.add(new atlas.layer.HeatMapLayer(datasource, null, {
  radius: 10,
  opacity: 0.8
}), 'labels');
```

Aqui está o exemplo de código completo em execução do código anterior.

<br/>

<iframe height='500' scrolling='no' title='Camada do mapa de calor simples' src='//codepen.io/azuremaps/embed/gQqdQB/?height=500&theme-id=0&default-tab=js,result&embed-version=2&editable=true' frameborder='no' allowtransparency='true' allowfullscreen='true' style='width: 100%;'>Consulte a <a href='https://codepen.io/azuremaps/pen/gQqdQB/'>camada do mapa de calor simples</a> da caneta pelo mapas do Azure (<a href='https://codepen.io/azuremaps'>@azuremaps</a>) em <a href='https://codepen.io'>CodePen</a>.
</iframe>

## <a name="customize-the-heat-map-layer"></a>Personalizar a camada do mapa de calor

O exemplo anterior personalizou o mapa de calor definindo as opções de raio e opacidade. A camada do mapa de calor fornece várias opções de personalização, incluindo:

* `radius`: define um raio de pixel no qual processar cada ponto de dados. Você pode definir o raio como um número fixo ou como uma expressão. Usando uma expressão, você pode dimensionar o raio com base no nível de zoom e representar uma área espacial consistente no mapa (por exemplo, um raio de 5 km).
* `color`: especifica como o mapa de calor é colorido. Um gradiente de cor é um recurso comum dos mapas de calor, e você pode obter o efeito com uma expressão `interpolate`. Você também pode usar uma expressão `step` para colorir o mapa de calor, dividindo a densidade visualmente em intervalos que se assemelham a um mapa de estilo de contorno ou de radar. Essas paletas de cores definem as cores do valor mínimo para o máximo de densidade. 

  Você especifica valores de cor para mapas de calor como uma expressão no valor `heatmap-density`. A cor no índice 0 em uma expressão de interpolação ou a cor padrão de uma expressão de etapa define a cor da área onde não há dados. Você pode usar isso para definir uma cor de plano de fundo. Geralmente, esse valor é definido como transparente ou um preto semitransparente. 
   
  Aqui estão exemplos de expressões de cor:

  | Expressão de cor de interpolação | Expressão de cor de nível | 
  |--------------------------------|--------------------------|
  | \[<br/>&nbsp;&nbsp;&nbsp;&nbsp;' interpolarize ',<br/>&nbsp;&nbsp;&nbsp;&nbsp;\[' linear '\],<br/>&nbsp;&nbsp;&nbsp;&nbsp;\[' calor-densidade '\],<br/>&nbsp;&nbsp;&nbsp;&nbsp;0, 'transparent',<br/>&nbsp;&nbsp;&nbsp;&nbsp;0, 1, ' roxo ',<br/>&nbsp;&nbsp;&nbsp;&nbsp;0.5, '#fb00fb',<br/>&nbsp;&nbsp;&nbsp;&nbsp;1, ' #00c3ff '<br/>\] | \[<br/>&nbsp;&nbsp;&nbsp;&nbsp;' etapa ',<br/>&nbsp;&nbsp;&nbsp;&nbsp;\[' calor-densidade '\],<br/>&nbsp;&nbsp;&nbsp;&nbsp;'transparent',<br/>&nbsp;&nbsp;&nbsp;&nbsp;0.01, 'navy',<br/>&nbsp;&nbsp;&nbsp;&nbsp;0,25, ' verde ',<br/>&nbsp;&nbsp;&nbsp;&nbsp;0,50, ' amarelo ',<br/>&nbsp;&nbsp;&nbsp;&nbsp;0,75, ' vermelho '<br/>\] |   

- `opacity`: especifica o quão opaco ou transparente a camada do mapa de calor é.
- `intensity`: aplica um multiplicador ao peso de cada ponto de dados para aumentar a intensidade geral do calor. Isso ajuda a tornar as pequenas diferenças no peso dos pontos de dados mais fáceis de Visualizar.
- `weight`: por padrão, todos os pontos de dados têm um peso de 1 e são ponderados igualmente. A opção Weight atua como um multiplicador e você pode defini-la como um número ou uma expressão. Se um número (por exemplo, 2) for definido como o peso, será o equivalente a colocar cada ponto de dados no mapa duas vezes, dobrando a densidade. Definir a opção de peso para um número renderiza o mapa de calor de forma semelhante ao uso da opção de intensidade. 

  No entanto, se você usar uma expressão, o peso de cada ponto de dados poderá ser baseado nas propriedades de cada ponto de dados. Por exemplo, suponha que cada ponto de dados representa um terremoto. Uma métrica importante de cada ponto de dados de terremoto é um valor de magnitude. Os terremotos acontecem o tempo todo, mas têm uma magnitude menor e nem mesmo se sentiram. Usando o valor de magnitude em uma expressão para atribuir o peso a cada ponto de dados, você obtém uma representação melhor da significância dos terremotos dentro do mapa de calor.
- `source` e `source-layer`: permite que você atualize a fonte de dados.

Aqui está uma ferramenta para testar as diferentes opções de camada do mapa de calor.

<br/>

<iframe height='700' scrolling='no' title='Opções da camada do mapa de calor' src='//codepen.io/azuremaps/embed/WYPaXr/?height=700&theme-id=0&default-tab=result' frameborder='no' allowtransparency='true' allowfullscreen='true' style='width: 100%;'>Consulte as <a href='https://codepen.io/azuremaps/pen/WYPaXr/'>Opções da camada do mapa de calor</a> da caneta pelo mapas do Azure (<a href='https://codepen.io/azuremaps'>@azuremaps</a>) em <a href='https://codepen.io'>CodePen</a>.
</iframe>

## <a name="consistent-zoomable-heat-map"></a>Mapa de calor com zoom consistente

Por padrão, o raios de pontos de dados renderizados na camada do mapa de calor têm um raio de pixel fixo para todos os níveis de zoom. À medida que você aplica zoom no mapa, os dados são agregados e a camada do mapa de calor é diferente. 

Use uma expressão `zoom` para dimensionar o raio para cada nível de zoom, de modo que cada ponto de dados cubra a mesma área física do mapa. Isso faz com que a camada do mapa de calor pareça mais estática e consistente. Cada nível de zoom do mapa tem duas vezes mais pixels vertical e horizontalmente como o nível de zoom anterior. 

Dimensionar o raio para que ele fique duplo com cada nível de zoom cria um mapa de calor que se parece consistente em todos os níveis de zoom. Para fazer isso, use a `zoom` com uma expressão de `exponential interpolation` base 2, conforme mostrado no exemplo a seguir. Aplique zoom no mapa para ver como o mapa de calor é dimensionado com o nível de zoom.

<br/>

<iframe height="500" style="width: 100%;" scrolling="no" title="Mapa de calor com zoom consistente" src="//codepen.io/azuremaps/embed/OGyMZr/?height=500&theme-id=0&default-tab=js,result" frameborder="no" allowtransparency="true" allowfullscreen="true">
Consulte o <a href='https://codepen.io/azuremaps/pen/OGyMZr/'>mapa de calor com zoom consistente</a> com a caneta pelo mapas do Azure (<a href='https://codepen.io/azuremaps'>@azuremaps</a>) em <a href='https://codepen.io'>CodePen</a>.
</iframe>

> [!TIP]
> Quando você habilita o clustering na fonte de dados, os pontos próximos uns dos outros são agrupados em conjunto como um ponto clusterizado. Você pode usar a contagem de pontos de cada cluster como a expressão de peso para o mapa de calor e reduzir significativamente o número de pontos que precisam ser renderizados. A contagem de pontos de um cluster é armazenada em uma propriedade `point_count` do recurso de ponto: 
> ```JavaScript
> var layer = new atlas.layer.HeatMapLayer(datasource, null, {
>    weight: ['get', 'point_count']
> });
> ```
> Se o raio de clustering for apenas alguns pixels, haverá pouca diferença visual na renderização. Um raio maior agrupa mais pontos em cada cluster e melhora o desempenho do calor.

## <a name="next-steps"></a>Passos seguintes

Saiba mais sobre as classes e os métodos usados neste artigo:

> [!div class="nextstepaction"]
> [HeatMapLayer](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.htmlmarker?view=azure-iot-typescript-latest)

> [!div class="nextstepaction"]
> [HeatMapLayerOptions](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.heatmaplayeroptions?view=azure-iot-typescript-latest)

Para obter mais exemplos de código a serem adicionados aos seus mapas, consulte os seguintes artigos:

> [!div class="nextstepaction"]
> [Criar uma fonte de dados](create-data-source-web-sdk.md)

> [!div class="nextstepaction"]
> [Usar expressões de estilo controladas por dados](data-driven-style-expressions-web-sdk.md)
