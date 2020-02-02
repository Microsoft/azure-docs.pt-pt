---
title: Adicionar uma camada do mapa de calor a um mapa | Mapas do Microsoft Azure
description: Neste artigo, você aprenderá como adicionar uma camada do mapa de calor a um mapa usando o SDK da Web do Microsoft Azure Maps.
author: rbrundritt
ms.author: richbrun
ms.date: 07/29/2019
ms.topic: conceptual
ms.service: azure-maps
services: azure-maps
manager: ''
ms.custom: codepen
ms.openlocfilehash: 98c8db4e4b866879a437d1ffad6668cbae42fcdf
ms.sourcegitcommit: fa6fe765e08aa2e015f2f8dbc2445664d63cc591
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 02/01/2020
ms.locfileid: "76933595"
---
# <a name="add-a-heat-map-layer"></a>Adicionar uma camada de mapa térmico

Os mapas de calor, também conhecidos como mapas de densidade de pontos, são um tipo de visualização de dados. São usados para representar a densidade de dados usando uma gama de cores e mostrar os dados "pontos quentes" num mapa. Os mapas de calor são uma ótima maneira de renderizar conjuntos de dados com um grande número de pontos. 

Renderizando dezenas de milhares de pontos como símbolos pode cobrir a maior parte da área do mapa. Este caso provavelmente resulta em muitos símbolos sobrepostos. Dificultando a compreensão dos dados. No entanto, visualizar este mesmo conjunto de dados como um mapa de calor torna mais fácil ver a densidade e a densidade relativa de cada ponto de dados.

Você pode usar mapas de calor em vários cenários diferentes, incluindo:

- **Dados de temperatura**: fornece aproximações para o que é a temperatura entre dois pontos de dados.
- **Dados para sensores**de ruído : Mostra não só a intensidade do ruído onde o sensor está, mas também pode fornecer informações sobre a dissipação ao longo de uma distância. O nível de ruído em um site pode não estar alto. Se a área de cobertura de ruído de vários sensores se sobrepor, é possível que esta área de sobreposição possa experimentar níveis de ruído mais elevados. Como tal, a área sobreposta seria visível no mapa de calor.
- **Rastreamento de GPS**: inclui a velocidade como um mapa de altura ponderado, em que a intensidade de cada ponto de dados é baseada na velocidade. Por exemplo, esta funcionalidade fornece uma forma de ver onde um veículo estava em excesso de velocidade.

> [!TIP]
> Por padrão, as camadas do mapa de calor renderizam as coordenadas de todas as geometrias em uma fonte de dados. Para limitar a camada de modo a que apenas torne características de geometria de ponto, coloque a propriedade `filter` da camada para `['==', ['geometry-type'], 'Point']`. Se quiser incluir também funcionalidades MultiPoint, defina a propriedade `filter` da camada para `['any', ['==', ['geometry-type'], 'Point'], ['==', ['geometry-type'], 'MultiPoint']]`.

<br/>

<iframe src="https://channel9.msdn.com/Shows/Internet-of-Things-Show/Heat-Maps-and-Image-Overlays-in-Azure-Maps/player" width="960" height="540" allowFullScreen frameBorder="0"></iframe>

## <a name="add-a-heat-map-layer"></a>Adicionar uma camada de mapa térmico

Para renderizar uma fonte de dados de pontos como um mapa de calor, passe sua fonte de dados para uma instância da classe `HeatMapLayer` e adicione-a ao mapa.

No código a seguir, cada ponto de calor tem um raio de 10 pixels em todos os níveis de zoom. Para garantir uma melhor experiência do utilizador, o mapa de calor está abaixo da camada de etiqueta. Os rótulos permanecem claramente visíveis. Os dados desta amostra são do [Programa usgs de risco de terramoto.](https://earthquake.usgs.gov/) É para terramotos significativos que ocorreram nos últimos 30 dias.

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
* `color`: especifica como o mapa de calor é colorido. Um gradiente de cor é uma característica comum dos mapas de calor. Pode-se conseguir o efeito com uma expressão `interpolate`. Você também pode usar uma expressão `step` para colorir o mapa de calor, dividindo a densidade visualmente em intervalos que se assemelham a um mapa de estilo de contorno ou de radar. Essas paletas de cores definem as cores do valor mínimo para o máximo de densidade. 

  Você especifica valores de cor para mapas de calor como uma expressão no valor `heatmap-density`. A cor da área onde não há dados é definida no índice 0 da expressão "Interpolation", ou na cor padrão de uma expressão "Pisada". Pode usar este valor para definir uma cor de fundo. Geralmente, esse valor é definido como transparente ou um preto semitransparente. 
   
  Aqui estão exemplos de expressões de cor:

  | Expressão de cor de interpolação | Expressão de cor de nível | 
  |--------------------------------|--------------------------|
  | \[<br/>&nbsp;&nbsp;&nbsp;&nbsp;' interpolarize ',<br/>&nbsp;&nbsp;&nbsp;&nbsp;\[\]"linear", ,<br/>&nbsp;&nbsp;&nbsp;&nbsp;\[\]de "densidade de mapas térmico",<br/>&nbsp;&nbsp;&nbsp;&nbsp;0, 'transparent',<br/>&nbsp;&nbsp;&nbsp;&nbsp;0, 1, ' roxo ',<br/>&nbsp;&nbsp;&nbsp;&nbsp;0.5, '#fb00fb',<br/>&nbsp;&nbsp;&nbsp;&nbsp;1, ' #00c3ff '<br/>\] | \[<br/>&nbsp;&nbsp;&nbsp;&nbsp;' etapa ',<br/>&nbsp;&nbsp;&nbsp;&nbsp;\[\]de "densidade de mapas térmico",<br/>&nbsp;&nbsp;&nbsp;&nbsp;'transparent',<br/>&nbsp;&nbsp;&nbsp;&nbsp;0.01, 'navy',<br/>&nbsp;&nbsp;&nbsp;&nbsp;0,25, ' verde ',<br/>&nbsp;&nbsp;&nbsp;&nbsp;0,50, ' amarelo ',<br/>&nbsp;&nbsp;&nbsp;&nbsp;0,75, ' vermelho '<br/>\] |   

- `opacity`: especifica o quão opaco ou transparente a camada do mapa de calor é.
- `intensity`: aplica um multiplicador ao peso de cada ponto de dados para aumentar a intensidade geral do calor. Isto ajuda a fazer a diferença no peso dos pontos de dados, facilitando a visualização.
- `weight`: por padrão, todos os pontos de dados têm um peso de 1 e são ponderados igualmente. A opção Weight atua como um multiplicador e você pode defini-la como um número ou uma expressão. Se um número é definido como o peso, é a equivalência de colocar cada ponto de dados no mapa duas vezes. Por exemplo, se o peso for 2, então a densidade duplica. Definir a opção de peso para um número renderiza o mapa de calor de forma semelhante ao uso da opção de intensidade. 

  No entanto, se você usar uma expressão, o peso de cada ponto de dados poderá ser baseado nas propriedades de cada ponto de dados. Por exemplo, suponha que cada ponto de dados representa um terramoto. Uma métrica importante de cada ponto de dados do terramoto tem sido um valor de magnitude. Terramotos acontecem a toda a hora, mas a maioria tem uma baixa magnitude, e não são notados. Usando o valor de magnitude em uma expressão para atribuir o peso a cada ponto de dados, você obtém uma representação melhor da significância dos terremotos dentro do mapa de calor.
- `source` e `source-layer`: permite que você atualize a fonte de dados.

Aqui está uma ferramenta para testar as diferentes opções de camada do mapa de calor.

<br/>

<iframe height='700' scrolling='no' title='Opções da camada do mapa de calor' src='//codepen.io/azuremaps/embed/WYPaXr/?height=700&theme-id=0&default-tab=result' frameborder='no' allowtransparency='true' allowfullscreen='true' style='width: 100%;'>Consulte as <a href='https://codepen.io/azuremaps/pen/WYPaXr/'>Opções da camada do mapa de calor</a> da caneta pelo mapas do Azure (<a href='https://codepen.io/azuremaps'>@azuremaps</a>) em <a href='https://codepen.io'>CodePen</a>.
</iframe>

## <a name="consistent-zoomable-heat-map"></a>Mapa de calor com zoom consistente

Por padrão, o raios de pontos de dados renderizados na camada do mapa de calor têm um raio de pixel fixo para todos os níveis de zoom. À medida que você aplica zoom no mapa, os dados são agregados e a camada do mapa de calor é diferente. 

Use uma expressão `zoom` para dimensionar o raio para cada nível de zoom, de modo que cada ponto de dados cubra a mesma área física do mapa. Esta expressão faz com que a camada do mapa de calor pareça mais estática e consistente. Cada nível de zoom do mapa tem duas vezes mais pixels vertical e horizontalmente como o nível de zoom anterior. 

Dimensionar o raio para que ele fique duplo com cada nível de zoom cria um mapa de calor que se parece consistente em todos os níveis de zoom. Para aplicar esta escala, utilize `zoom` com uma expressão `exponential interpolation` base 2, como mostra a amostra seguinte. Aplique zoom no mapa para ver como o mapa de calor é dimensionado com o nível de zoom.

<br/>

<iframe height="500" style="width: 100%;" scrolling="no" title="Mapa de calor com zoom consistente" src="//codepen.io/azuremaps/embed/OGyMZr/?height=500&theme-id=0&default-tab=js,result" frameborder="no" allowtransparency="true" allowfullscreen="true">
Consulte o <a href='https://codepen.io/azuremaps/pen/OGyMZr/'>mapa de calor com zoom consistente</a> com a caneta pelo mapas do Azure (<a href='https://codepen.io/azuremaps'>@azuremaps</a>) em <a href='https://codepen.io'>CodePen</a>.
</iframe>

> [!TIP]
> Quando você habilita o clustering na fonte de dados, os pontos próximos uns dos outros são agrupados em conjunto como um ponto clusterizado. Pode utilizar a contagem de pontos de cada cluster como expressão de peso para o mapa de calor. Isto pode reduzir significativamente o número de pontos a render. A contagem de pontos de um cluster é armazenada numa propriedade `point_count` da característica do ponto: 

> ```JavaScript
> var layer = new atlas.layer.HeatMapLayer(datasource, null, {
>    weight: ['get', 'point_count']
> });
> ```

> Se o raio de agrupamento for apenas alguns pixels, haveria uma pequena diferença visual na renderização. Um raio maior agrupa mais pontos em cada cluster e melhora o desempenho do calor.

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
