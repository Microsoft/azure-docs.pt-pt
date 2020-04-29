---
title: Adicione uma camada de mapa de calor a um mapa / Microsoft Azure Maps
description: Neste artigo, você aprenderá sobre como adicionar uma camada de mapa de calor a um mapa usando o Microsoft Azure Maps Web SDK.
author: rbrundritt
ms.author: richbrun
ms.date: 07/29/2019
ms.topic: conceptual
ms.service: azure-maps
services: azure-maps
manager: ''
ms.custom: codepen
ms.openlocfilehash: 19765bd28f365cc6f6d5b06646896613dd3e3e87
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/28/2020
ms.locfileid: "80804559"
---
# <a name="add-a-heat-map-layer"></a>Adicionar uma camada de mapa térmico

Os mapas de calor, também conhecidos como mapas de densidade de pontos, são um tipo de visualização de dados. São usados para representar a densidade de dados usando uma gama de cores e mostrar os dados "pontos quentes" num mapa. Os mapas de calor são uma ótima maneira de renderizar conjuntos de dados com um grande número de pontos. 

Renderizando dezenas de milhares de pontos como símbolos pode cobrir a maior parte da área do mapa. Este caso provavelmente resulta em muitos símbolos sobrepostos. Dificultando a compreensão dos dados. No entanto, visualizar este mesmo conjunto de dados como um mapa de calor torna mais fácil ver a densidade e a densidade relativa de cada ponto de dados.

Você pode usar mapas de calor em muitos cenários diferentes, incluindo:

- **Dados da temperatura**: Fornece aproximações para o que a temperatura está entre dois pontos de dados.
- **Dados para sensores**de ruído : Mostra não só a intensidade do ruído onde o sensor está, mas também pode fornecer informações sobre a dissipação ao longo de uma distância. O nível de ruído em qualquer local pode não ser alto. Se a área de cobertura de ruído de vários sensores se sobrepor, é possível que esta área de sobreposição possa experimentar níveis de ruído mais elevados. Como tal, a área sobreposta seria visível no mapa de calor.
- **Traço de GPS**: Inclui a velocidade como mapa de altura ponderado, onde a intensidade de cada ponto de dados é baseada na velocidade. Por exemplo, esta funcionalidade fornece uma forma de ver onde um veículo estava em excesso de velocidade.

> [!TIP]
> As camadas de mapas de calor por padrão tornam as coordenadas de todas as geometrias numa fonte de dados. Para limitar a camada de modo a que apenas `filter` torne características `['==', ['geometry-type'], 'Point']`de geometria de ponto, coloque a propriedade da camada para . Se quiser incluir também funcionalidades MultiPoint, defina `filter` `['any', ['==', ['geometry-type'], 'Point'], ['==', ['geometry-type'], 'MultiPoint']]`a propriedade da camada para .

<br/>

<iframe src="https://channel9.msdn.com/Shows/Internet-of-Things-Show/Heat-Maps-and-Image-Overlays-in-Azure-Maps/player" width="960" height="540" allowFullScreen frameBorder="0"></iframe>

## <a name="add-a-heat-map-layer"></a>Adicionar uma camada de mapa térmico

Para tornar uma fonte de dados de pontos como um `HeatMapLayer` mapa de calor, passe a sua fonte de dados para uma instância da classe e adicione-a ao mapa.

No seguinte código, cada ponto de calor tem um raio de 10 pixels em todos os níveis de zoom. Para garantir uma melhor experiência do utilizador, o mapa de calor está abaixo da camada de etiqueta. Os rótulos permanecem claramente visíveis. Os dados desta amostra são do [Programa usgs de risco de terramoto.](https://earthquake.usgs.gov/) É para terramotos significativos que ocorreram nos últimos 30 dias.

```javascript
//Create a data source and add it to the map.
var datasource = new atlas.source.DataSource();
map.sources.add(datasource);

//Load a dataset of points, in this case earthquake data from the USGS.
datasource.importDataFromUrl('https://earthquake.usgs.gov/earthquakes/feed/v1.0/summary/all_week.geojson');

//Create a heat map and add it to the map.
map.layers.add(new atlas.layer.HeatMapLayer(datasource, null, {
  radius: 10,
  opacity: 0.8
}), 'labels');
```

Aqui está a amostra completa do código de execução do código anterior.

<br/>

<iframe height='500' scrolling='no' title='Camada simples do mapa de calor' src='//codepen.io/azuremaps/embed/gQqdQB/?height=500&theme-id=0&default-tab=js,result&embed-version=2&editable=true' frameborder='no' allowtransparency='true' allowfullscreen='true' style='width: 100%;'>Veja a <a href='https://codepen.io/azuremaps/pen/gQqdQB/'>camada de mapa</a> de<a href='https://codepen.io/azuremaps'>@azuremaps</a>calor simples pen por Azure Maps () em <a href='https://codepen.io'>CodePen</a>.
</iframe>

## <a name="customize-the-heat-map-layer"></a>Personalize a camada do mapa de calor

O exemplo anterior personalizou o mapa de calor definindo as opções de raio e opacidade. A camada de mapa de calor fornece várias opções para personalização, incluindo:

* `radius`: Define um raio de pixel para render cada ponto de dados. Pode definir o raio como um número fixo ou como expressão. Ao utilizar uma expressão, pode escalar o raio com base no nível de zoom, e representar uma área espacial consistente no mapa (por exemplo, um raio de 8 km).
* `color`: Especifica como o mapa de calor é colorido. Um gradiente de cor é uma característica comum dos mapas de calor. Pode-se conseguir o `interpolate` efeito com uma expressão. Também pode usar `step` uma expressão para colorir o mapa de calor, rompendo a densidade visualmente em intervalos que se assemelham a um contorno ou mapa de estilo de radar. Estas paletas de cores definem as cores do mínimo ao valor de densidade máxima. 

  Especifica os valores de cor para `heatmap-density` mapas de calor como expressão sobre o valor. A cor da área onde não há dados é definida no índice 0 da expressão "Interpolation", ou na cor padrão de uma expressão "Pisada". Pode usar este valor para definir uma cor de fundo. Muitas vezes, este valor é definido para transparente, ou um preto semi-transparente. 
   
  Aqui estão exemplos de expressões de cores:

  | Expressão de cor de interpolação | Expressão de cor pisada | 
  |--------------------------------|--------------------------|
  | \[<br/>&nbsp;&nbsp;&nbsp;&nbsp;"interpolado",<br/>&nbsp;&nbsp;&nbsp;&nbsp;\["linear",\]<br/>&nbsp;&nbsp;&nbsp;&nbsp;\[«densidade de mapa\]térmico»,<br/>&nbsp;&nbsp;&nbsp;&nbsp;0, "transparente",<br/>&nbsp;&nbsp;&nbsp;&nbsp;0.01, 'roxo',<br/>&nbsp;&nbsp;&nbsp;&nbsp;0.5, '#fb00fb',<br/>&nbsp;&nbsp;&nbsp;&nbsp;1, '#00c3ff'<br/>\] | \[<br/>&nbsp;&nbsp;&nbsp;&nbsp;"passo",<br/>&nbsp;&nbsp;&nbsp;&nbsp;\[«densidade de mapa\]térmico»,<br/>&nbsp;&nbsp;&nbsp;&nbsp;"transparente",<br/>&nbsp;&nbsp;&nbsp;&nbsp;0.01, 'marinha',<br/>&nbsp;&nbsp;&nbsp;&nbsp;0.25, 'verde',<br/>&nbsp;&nbsp;&nbsp;&nbsp;0.50, 'amarelo',<br/>&nbsp;&nbsp;&nbsp;&nbsp;0.75, 'vermelho'<br/>\] |   

- `opacity`: Especifica o quão opaco ou transparente é a camada do mapa de calor.
- `intensity`: Aplica um multiplicador ao peso de cada ponto de dados para aumentar a intensidade global do mapa térmico. Causa uma diferença no peso dos pontos de dados, facilitando a visualização.
- `weight`: Por predefinição, todos os pontos de dados têm um peso de 1, e são igualmente ponderados. A opção de peso funciona como um multiplicador, e pode defini-la como um número ou uma expressão. Se um número é definido como o peso, é a equivalência de colocar cada ponto de dados no mapa duas vezes. Por exemplo, se o peso for 2, então a densidade duplica. Definir a opção de peso para um número torna o mapa de calor de forma semelhante à utilização da opção de intensidade. 

  No entanto, se utilizar uma expressão, o peso de cada ponto de dados pode basear-se nas propriedades de cada ponto de dados. Por exemplo, suponha que cada ponto de dados representa um terramoto. O valor de magnitude tem sido uma métrica importante para cada ponto de dados do terramoto. Terramotos acontecem a toda a hora, mas a maioria tem uma baixa magnitude, e não são notados. Utilize o valor de magnitude numa expressão para atribuir o peso a cada ponto de dados. Ao usar o valor de magnitude para atribuir o peso, obtém-se uma melhor representação do significado dos terramotos dentro do mapa de calor.
- `source`: `source-layer`Permitir-lhe atualizar a fonte de dados.

Aqui está uma ferramenta para testar as diferentes opções de camada de mapa de calor.

<br/>

<iframe height='700' scrolling='no' title='Opções de camada de mapa de calor' src='//codepen.io/azuremaps/embed/WYPaXr/?height=700&theme-id=0&default-tab=result' frameborder='no' allowtransparency='true' allowfullscreen='true' style='width: 100%;'>Consulte as <a href='https://codepen.io/azuremaps/pen/WYPaXr/'>opções</a> de camada de<a href='https://codepen.io/azuremaps'>@azuremaps</a>gráfico de calor pen por Azure Maps () em <a href='https://codepen.io'>CodePen</a>.
</iframe>

## <a name="consistent-zoomable-heat-map"></a>Mapa de calor zoomável consistente

Por padrão, o rádio dos pontos de dados renderizados na camada do mapa de calor tem um raio de pixel fixo para todos os níveis de zoom. À medida que amplia o mapa, os dados agregam-se e a camada do mapa de calor parece diferente. 

Utilize `zoom` uma expressão para escalar o raio para cada nível de zoom, de modo a que cada ponto de dados cubra a mesma área física do mapa. Esta expressão faz com que a camada do mapa de calor pareça mais estática e consistente. Cada nível de zoom do mapa tem o dobro de pixels vertical e horizontalmente do que o nível de zoom anterior. 

Dimensionar o raio de modo a duplicar a cada nível de zoom cria um mapa de calor que parece consistente em todos os níveis de zoom. Para aplicar esta escala, `zoom` utilize com `exponential interpolation` uma expressão base 2, com o raio de pixel definido para `2 * Math.pow(2, minZoom - maxZoom)` o nível mínimo de zoom e um raio escalado para o nível máximo de zoom calculado como mostrado na amostra seguinte. Zoom o mapa para ver como o mapa de calor escala com o nível de zoom.

<br/>

<iframe height="500" style="width: 100%;" scrolling="no" title="Mapa de calor zoomável consistente" src="//codepen.io/azuremaps/embed/OGyMZr/?height=500&theme-id=0&default-tab=js,result&editable=true" frameborder="no" allowtransparency="true" allowfullscreen="true">
Veja o mapa de <a href='https://codepen.io/azuremaps/pen/OGyMZr/'>calor zoomável</a> <a href='https://codepen.io/azuremaps'>@azuremaps</a>pen consistente por Azure Maps () em <a href='https://codepen.io'>CodePen</a>.
</iframe>

> [!TIP]
> Quando permite o agrupamento na fonte de dados, os pontos que estão próximos uns dos outros são agrupados como um ponto agrupado. Pode utilizar a contagem de pontos de cada cluster como expressão de peso para o mapa de calor. Isto pode reduzir significativamente o número de pontos a render. A contagem de pontos de `point_count` um cluster é armazenada numa propriedade da característica do ponto: 
> ```JavaScript
> var layer = new atlas.layer.HeatMapLayer(datasource, null, {
>    weight: ['get', 'point_count']
> });
> ```
> Se o raio de agrupamento for apenas alguns pixels, haveria uma pequena diferença visual na renderização. Um raio maior agrupa mais pontos em cada cluster, e melhora o desempenho do mapa de calor.

## <a name="next-steps"></a>Passos seguintes

Saiba mais sobre as aulas e métodos utilizados neste artigo:

> [!div class="nextstepaction"]
> [HeatMapLayer](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.htmlmarker?view=azure-iot-typescript-latest)

> [!div class="nextstepaction"]
> [Opções de HeatMapLayer](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.heatmaplayeroptions?view=azure-iot-typescript-latest)

Para mais exemplos de código para adicionar aos seus mapas, consulte os seguintes artigos:

> [!div class="nextstepaction"]
> [Criar uma origem de dados](create-data-source-web-sdk.md)

> [!div class="nextstepaction"]
> [Utilizar expressões de estilo com base em dados](data-driven-style-expressions-web-sdk.md)
