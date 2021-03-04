---
title: Adicione uma camada de mapa de calor aos mapas android | Microsoft Azure Maps
description: Aprenda a criar um mapa de calor. Veja como usar o Azure MapsAndroid SDK para adicionar uma camada de mapa de calor a um mapa. Descubra como personalizar as camadas de mapas de calor.
author: rbrundritt
ms.author: richbrun
ms.date: 02/26/2021
ms.topic: conceptual
ms.service: azure-maps
services: azure-maps
manager: cpendle
zone_pivot_groups: azure-maps-android
ms.openlocfilehash: fce2c2d007f92c43e763826f9345f773324e885e
ms.sourcegitcommit: 4b7a53cca4197db8166874831b9f93f716e38e30
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/04/2021
ms.locfileid: "102100190"
---
# <a name="add-a-heat-map-layer-android-sdk"></a>Adicione uma camada de mapa de calor (Android SDK)

Os mapas de calor, também conhecidos como mapas de densidade de pontos, são um tipo de visualização de dados. São usados para representar a densidade de dados usando uma gama de cores e mostrar os dados "hot spots" num mapa. Os mapas de calor são uma ótima maneira de render conjuntos de dados com um grande número de pontos. 

Renderizar dezenas de milhares de pontos como símbolos pode cobrir a maior parte da área do mapa. Este caso provavelmente resulta em muitos símbolos sobrepostos. Dificultando a compreensão dos dados. No entanto, visualizar este mesmo conjunto de dados como um mapa de calor torna fácil ver a densidade e a densidade relativa de cada ponto de dados.

Você pode usar mapas de calor em vários cenários diferentes, incluindo:

- **Dados da temperatura**: Fornece aproximações para o que a temperatura está entre dois pontos de dados.
- **Dados para sensores de ruído**: Mostra não só a intensidade do ruído onde está o sensor, mas também pode fornecer informações sobre a dissipação à distância. O nível de ruído em qualquer local pode não ser alto. Se a área de cobertura de ruído de vários sensores se sobrepor, é possível que esta área sobreposta possa experimentar níveis de ruído mais elevados. Como tal, a área sobreposta seria visível no mapa de calor.
- **Traço GPS**: Inclui a velocidade como um mapa de altura ponderado, onde a intensidade de cada ponto de dados é baseada na velocidade. Por exemplo, esta funcionalidade fornece uma maneira de ver onde um veículo estava em excesso de velocidade.

> [!TIP]
> As camadas de mapas de calor por padrão tornam as coordenadas de todas as geometrias numa fonte de dados. Para limitar a camada de modo a que apenas torne as características de geometria do ponto, deite `filter` a opção da camada para `eq(geometryType(), "Point")` . Se também pretender incluir funcionalidades MultiPoint, deite `filter` a opção da camada para `any(eq(geometryType(), "Point"), eq(geometryType(), "MultiPoint"))` .

</br>

> [!VIDEO https://channel9.msdn.com/Shows/Internet-of-Things-Show/Heat-Maps-and-Image-Overlays-in-Azure-Maps/player?format=ny]

## <a name="prerequisites"></a>Pré-requisitos

Certifique-se de completar os passos no [Quickstart: Criar um documento de aplicação Android.](quick-android-map.md) Os blocos de código deste artigo podem ser inseridos no manipulador de eventos de `onReady` mapas.

## <a name="add-a-heat-map-layer"></a>Adicionar uma camada de mapa térmico

Para tornar uma fonte de dados de pontos como um mapa de calor, passe a sua fonte de dados para um caso da `HeatMapLayer` classe, e adicione-a ao mapa.

A seguinte amostra de código carrega um feed GeoJSON de terramotos da semana passada e torna-os como um mapa de calor. Cada ponto de dados é renderizado com um raio de 10 pixels em todos os níveis de zoom. Para garantir uma melhor experiência do utilizador, o mapa de calor está abaixo da camada de etiqueta para que as etiquetas permaneçam claramente visíveis. Os dados desta amostra são do [Programa usgs de riscos de terramoto.](https://earthquake.usgs.gov/) Esta amostra carrega dados da GeoJSON da web utilizando o bloco de código de utilidade de importação de dados fornecido no [Documento de Fonte de Dados.](create-data-source-android-sdk.md)

::: zone pivot="programming-language-java-android"

```java
//Create a data source and add it to the map.
DataSource source = new DataSource();
map.sources.add(source);

//Create a heat map layer.
HeatMapLayer layer = new HeatMapLayer(source,
  heatmapRadius(10f),
  heatmapOpacity(0.8f)
);

//Add the layer to the map, below the labels.
map.layers.add(layer, "labels");

//Import the geojson data and add it to the data source.
Utils.importData("https://earthquake.usgs.gov/earthquakes/feed/v1.0/summary/all_week.geojson",
    this,
    (String result) -> {
        //Parse the data as a GeoJSON Feature Collection.
        FeatureCollection fc = FeatureCollection.fromJson(result);

        //Add the feature collection to the data source.
        source.add(fc);

        //Optionally, update the maps camera to focus in on the data.

        //Calculate the bounding box of all the data in the Feature Collection.
        BoundingBox bbox = MapMath.fromData(fc);

        //Update the maps camera so it is focused on the data.
        map.setCamera(
            bounds(bbox),
            padding(20));
    });
```

::: zone-end

::: zone pivot="programming-language-kotlin"

```kotlin
//Create a data source and add it to the map.
val source = DataSource()
map.sources.add(source)

//Create a heat map layer.
val layer = HeatMapLayer(
    source,
    heatmapRadius(10f),
    heatmapOpacity(0.8f)
)

//Add the layer to the map, below the labels.
map.layers.add(layer, "labels")

//Import the geojson data and add it to the data source.
Utils.importData("https://earthquake.usgs.gov/earthquakes/feed/v1.0/summary/all_week.geojson",
    this
) { result: String? ->
    //Parse the data as a GeoJSON Feature Collection.
    val fc = FeatureCollection.fromJson(result!!)

    //Add the feature collection to the data source.
    source.add(fc)

    //Optionally, update the maps camera to focus in on the data.
    //Calculate the bounding box of all the data in the Feature Collection.
    val bbox = MapMath.fromData(fc)

    //Update the maps camera so it is focused on the data.
    map.setCamera(
        bounds(bbox),
        padding(20)
    )
}
```

::: zone-end

A imagem que se segue mostra um mapa a carregar um mapa de calor utilizando o código acima.

![Mapa com camada de mapa de calor de terramotos recentes](media/map-add-heat-map-layer-android/android-heat-map-layer.png)

## <a name="customize-the-heat-map-layer"></a>Personalize a camada do mapa de calor

O exemplo anterior personalizou o mapa de calor definindo as opções de raio e opacidade. A camada de mapa de calor fornece várias opções para personalização, incluindo:

- `heatmapRadius`: Define um raio de pixel para tornar cada ponto de dados. Pode definir o raio como um número fixo ou como uma expressão. Ao utilizar uma expressão, pode escalar o raio com base no nível de zoom e representar uma área espacial consistente no mapa (por exemplo, um raio de 8 km).
- `heatmapColor`: Especifica como o mapa de calor é colorido. Um gradiente de cor é uma característica comum dos mapas de calor. Pode conseguir o efeito com uma `interpolate` expressão. Você também pode usar uma `step` expressão para colorir o mapa de calor, quebrando a densidade visualmente em gamas que se assemelham a um mapa de contorno ou estilo radar. Estas paletas de cores definem as cores do mínimo para o valor máximo de densidade.

  Especifica os valores de cor para mapas de calor como uma expressão sobre o `heatmapDensity` valor. A cor da área onde não há dados é definida no índice 0 da expressão "Interpolação", ou na cor padrão de uma expressão "Pisar". Pode usar este valor para definir uma cor de fundo. Muitas vezes, este valor é definido para transparente, ou um preto semi-transparente. 

  Aqui estão exemplos de expressões de cores:

  | Expressão de cor interpolação | Expressão de cor pisada |
  |--------------------------------|--------------------------|
  | interpolado.<br/>&nbsp;&nbsp;&nbsp;&nbsp;linear(), <br/>&nbsp;&nbsp;&nbsp;&nbsp;heatmapDensity(),<br/>&nbsp;&nbsp;&nbsp;&nbsp;stop(0, cor (Color.TRANSPARENTE)),<br/>&nbsp;&nbsp;&nbsp;&nbsp;stop(0.01, cor (Color.MAGENTA)),<br/>&nbsp;&nbsp;&nbsp;&nbsp;stop(0.5, cor(parseColor("#fb00fb")),<br/>&nbsp;&nbsp;&nbsp;&nbsp;stop(1, cor(parseColor("#00c3ff"))<br/>)` | passo.<br/>&nbsp;&nbsp;&nbsp;&nbsp;heatmapDensity(),<br/>&nbsp;&nbsp;&nbsp;&nbsp;cor (Color.TRANSPARENTE),<br/>&nbsp;&nbsp;&nbsp;&nbsp;stop(0.01, cor(parseColor("#000080")),<br/>&nbsp;&nbsp;&nbsp;&nbsp;stop(0.25, cor(parseColor("#000080")),<br/>&nbsp;&nbsp;&nbsp;&nbsp;paragem (0.5, cor (Color.GREEN)),<br/>&nbsp;&nbsp;&nbsp;&nbsp;paragem (0.5, cor (Cor.YELLOW)),<br/>&nbsp;&nbsp;&nbsp;&nbsp;paragem (1, cor (Color.RED))<br/>) |

- `heatmapOpacity`: Especifica o quão opaca ou transparente é a camada do mapa de calor.
- `heatmapIntensity`: Aplica um multiplicador ao peso de cada ponto de dados para aumentar a intensidade total do mapa de calor. Causa uma diferença no peso dos pontos de dados, tornando mais fácil a visualização.
- `heatmapWeight`: Por predefinição, todos os pontos de dados têm um peso de 1, e são ponderados igualmente. A opção de peso funciona como um multiplicador, e pode defini-lo como um número ou uma expressão. Se um número é definido como o peso, é a equivalência de colocar cada ponto de dados no mapa duas vezes. Por exemplo, se o peso `2` for, então a densidade duplica. Definir a opção de peso para um número torna o mapa de calor de forma semelhante à utilização da opção de intensidade.

  No entanto, se utilizar uma expressão, o peso de cada ponto de dados pode basear-se nas propriedades de cada ponto de dados. Por exemplo, suponha que cada ponto de dados represente um terramoto. O valor de magnitude tem sido uma métrica importante para cada ponto de dados do terramoto. Os terramotos acontecem a toda a hora, mas a maioria tem uma baixa magnitude, e não são notados. Utilize o valor de magnitude numa expressão para atribuir o peso a cada ponto de dados. Ao usar o valor de magnitude para atribuir o peso, obtém-se uma melhor representação da importância dos terramotos dentro do mapa de calor.
- `minZoom` e `maxZoom` : A gama de nível de zoom onde a camada deve ser visualizada.
- `filter`: Expressão de filtro utilizada para limitar a recuperação da fonte e renderizada na camada.
- `sourceLayer`: Se a fonte de dados ligada à camada for uma fonte de azulejos vetoriais, deve ser especificada uma camada de origem dentro dos azulejos vetoriais.
- `visible`: Esconde ou mostra a camada.

Este é um exemplo de um mapa de calor onde uma expressão de interpolação de liner é usada para criar um gradiente de cor suave. A `mag` propriedade definida nos dados é utilizada com uma interpolação exponencial para definir o peso ou relevância de cada ponto de dados.

::: zone pivot="programming-language-java-android"

```java
HeatMapLayer layer = new HeatMapLayer(source,
    heatmapRadius(10f),

    //A linear interpolation is used to create a smooth color gradient based on the heat map density.
    heatmapColor(
        interpolate(
            linear(),
            heatmapDensity(),
            stop(0, color(Color.TRANSPARENT)),
            stop(0.01, color(Color.BLACK)),
            stop(0.25, color(Color.MAGENTA)),
            stop(0.5, color(Color.RED)),
            stop(0.75, color(Color.YELLOW)),
            stop(1, color(Color.WHITE))
        )
    ),

    //Using an exponential interpolation since earthquake magnitudes are on an exponential scale.
    heatmapWeight(
       interpolate(
            exponential(2),
            get("mag"),
            stop(0,0),

            //Any earthquake above a magnitude of 6 will have a weight of 1
            stop(6, 1)
       )
    )
);
```

::: zone-end

::: zone pivot="programming-language-kotlin"

```kotlin
val layer = HeatMapLayer(source,
    heatmapRadius(10f),

    //A linear interpolation is used to create a smooth color gradient based on the heat map density.
    heatmapColor(
        interpolate(
            linear(),
            heatmapDensity(),
            stop(0, color(Color.TRANSPARENT)),
            stop(0.01, color(Color.BLACK)),
            stop(0.25, color(Color.MAGENTA)),
            stop(0.5, color(Color.RED)),
            stop(0.75, color(Color.YELLOW)),
            stop(1, color(Color.WHITE))
        )
    ),

    //Using an exponential interpolation since earthquake magnitudes are on an exponential scale.
    heatmapWeight(
       interpolate(
            exponential(2),
            get("mag"),
            stop(0,0),

            //Any earthquake above a magnitude of 6 will have a weight of 1
            stop(6, 1)
       )
    )
)
```

::: zone-end

A imagem a seguir mostra a camada de mapa de calor personalizado acima usando os mesmos dados do exemplo do mapa de calor anterior.

![Mapa com camada de mapa de calor personalizado de terramotos recentes](media/map-add-heat-map-layer-android/android-custom-heat-map-layer.png)

## <a name="consistent-zoomable-heat-map"></a>Mapa de calor zoomável consistente

Por padrão, o raio dos pontos de dados renderizados na camada do mapa de calor tem um raio de pixel fixo para todos os níveis de zoom. À medida que se faz zoom no mapa, os dados agregam-se e a camada do mapa de calor parece diferente. O vídeo que se segue mostra o comportamento padrão do mapa de calor onde mantém um raio de pixel ao fazer zoom no mapa.

![Animação mostrando um zoom de mapa com uma camada de mapa de calor mostrando um tamanho de pixel consistente](media/map-add-heat-map-layer-android/android-heat-map-layer-zoom.gif)

Utilize uma `zoom` expressão para escalar o raio para cada nível de zoom, de modo que cada ponto de dados cubra a mesma área física do mapa. Esta expressão faz com que a camada do mapa de calor pareça mais estática e consistente. Cada nível de zoom do mapa tem o dobro dos pixéis vertical e horizontalmente do nível de zoom anterior.

Escalar o raio de modo que duplique com cada nível de zoom cria um mapa de calor que parece consistente em todos os níveis de zoom. Para aplicar este escalonamento, utilize `zoom` com uma expressão base 2, com o raio de pixel definido para o `exponential interpolation` nível mínimo de zoom e um raio de escala para o nível máximo de zoom calculado `2 * Math.pow(2, minZoom - maxZoom)` como indicado na amostra seguinte. Faça um zoom no mapa para ver como o mapa de calor escala com o nível de zoom.

::: zone pivot="programming-language-java-android"

```java
HeatMapLayer layer = new HeatMapLayer(source,
  heatmapRadius(
    interpolate(
      exponential(2),
      zoom(),

      //For zoom level 1 set the radius to 2 pixels.
      stop(1, 2f),

      //Between zoom level 1 and 19, exponentially scale the radius from 2 pixels to 2 * (maxZoom - minZoom)^2 pixels.
      stop(19, Math.pow(2, 19 - 1) * 2f)
    )
  ),
  heatmapOpacity(0.75f)
);
```

::: zone-end

::: zone pivot="programming-language-kotlin"

```kotlin
val layer = HeatMapLayer(source,
  heatmapRadius(
    interpolate(
      exponential(2),
      zoom(),

      //For zoom level 1 set the radius to 2 pixels.
      stop(1, 2f),

      //Between zoom level 1 and 19, exponentially scale the radius from 2 pixels to 2 * (maxZoom - minZoom)^2 pixels.
      stop(19, Math.pow(2.0, 19 - 1.0) * 2f)
    )
  ),
  heatmapOpacity(0.75f)
)
```

::: zone-end

O vídeo a seguir mostra um mapa que executa o código acima, que escala o raio enquanto o mapa está a ser ampliado para criar um mapa de calor consistente que renderiza através dos níveis de zoom.

![Animação mostrando um zoom de mapa com uma camada de mapa de calor mostrando um tamanho geoespacial consistente](media/map-add-heat-map-layer-android/android-consistent-zoomable-heat-map-layer.gif)

## <a name="next-steps"></a>Passos seguintes

Para obter mais exemplos de código para adicionar aos seus mapas, consulte os seguintes artigos:

> [!div class="nextstepaction"]
> [Criar uma origem de dados](create-data-source-android-sdk.md)

> [!div class="nextstepaction"]
> [Utilizar expressões de estilo com base em dados](data-driven-style-expressions-android-sdk.md)
