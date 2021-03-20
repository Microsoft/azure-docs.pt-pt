---
title: Adicione uma camada de polígono aos mapas Android | Microsoft Azure Maps
description: Aprenda a adicionar polígonos ou círculos aos mapas. Veja como usar o Azure Maps Android SDK para personalizar formas geométricas e torná-las fáceis de atualizar e manter.
author: rbrundritt
ms.author: richbrun
ms.date: 2/26/2021
ms.topic: conceptual
ms.service: azure-maps
services: azure-maps
manager: cpendle
zone_pivot_groups: azure-maps-android
ms.openlocfilehash: 68d68424e71bcf60bf504ae174b84b9c361b8637
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/20/2021
ms.locfileid: "102097266"
---
# <a name="add-a-polygon-layer-to-the-map-android-sdk"></a>Adicione uma camada de polígono ao mapa (Android SDK)

Este artigo mostra-lhe como tornar as áreas `Polygon` e `MultiPolygon` características geometrias no mapa usando uma camada de polígono.

## <a name="prerequisites"></a>Pré-requisitos

Certifique-se de completar os passos no [Quickstart: Criar um documento de aplicação Android.](quick-android-map.md) Os blocos de código deste artigo podem ser inseridos no manipulador de eventos de `onReady` mapas.

## <a name="use-a-polygon-layer"></a>Use uma camada de polígono

Quando uma camada de polígono é ligada a uma fonte de dados e carregada no mapa, torna a área com `Polygon` e `MultiPolygon` características. Para criar um polígono, adicione-o a uma fonte de dados e torne-o com uma camada de polígono utilizando a `PolygonLayer` classe.

::: zone pivot="programming-language-java-android"

```java
//Create a data source and add it to the map.
DataSource source = new DataSource();
map.sources.add(source);

//Create a rectangular polygon.
source.add(Polygon.fromLngLats(
    Arrays.asList(
        Arrays.asList(
            Point.fromLngLat(-73.98235, 40.76799),
            Point.fromLngLat(-73.95785, 40.80044),
            Point.fromLngLat(-73.94928, 40.79680),
            Point.fromLngLat(-73.97317, 40.76437),
            Point.fromLngLat(-73.98235, 40.76799)
        )
    )
));

//Create and add a polygon layer to render the polygon on the map, below the label layer.
map.layers.add(new PolygonLayer(source, 
    fillColor("red"),
    fillOpacity(0.7f)
), "labels");
```

::: zone-end

::: zone pivot="programming-language-kotlin"

```kotlin
//Create a data source and add it to the map.
val source = DataSource()
map.sources.add(source)

//Create a rectangular polygon.
source.add(
    Polygon.fromLngLats(
        Arrays.asList(
            Arrays.asList(
                Point.fromLngLat(-73.98235, 40.76799),
                Point.fromLngLat(-73.95785, 40.80044),
                Point.fromLngLat(-73.94928, 40.79680),
                Point.fromLngLat(-73.97317, 40.76437),
                Point.fromLngLat(-73.98235, 40.76799)
            )
        )
    )
)

//Create and add a polygon layer to render the polygon on the map, below the label layer.
map.layers.add(
    PolygonLayer(
        source,
        fillColor("red"),
        fillOpacity(0.7f)
    ), "labels"
)
```

::: zone-end

A imagem que se segue mostra o código acima, tornando a área de um polígono utilizando uma camada de polígono.

![Polígono com a sua área de enchimento renderizada](media/how-to-add-shapes-to-android-map/android-polygon-layer.png)

## <a name="use-a-polygon-and-line-layer-together"></a>Use um polígono e uma camada de linha juntos

Uma camada de linha é usada para tornar o contorno dos polígonos. A amostra de código a seguir torna um polígono como o exemplo anterior, mas agora adiciona uma camada de linha. Esta camada de linha é uma segunda camada ligada à fonte de dados.

::: zone pivot="programming-language-java-android"

```java
//Create a data source and add it to the map.
DataSource source = new DataSource();
map.sources.add(source);

//Create a rectangular polygon.
source.add(Polygon.fromLngLats(
    Arrays.asList(
        Arrays.asList(
            Point.fromLngLat(-73.98235, 40.76799),
            Point.fromLngLat(-73.95785, 40.80044),
            Point.fromLngLat(-73.94928, 40.79680),
            Point.fromLngLat(-73.97317, 40.76437),
            Point.fromLngLat(-73.98235, 40.76799)
        )
    )
));

//Create and add a polygon layer to render the polygon on the map, below the label layer.
map.layers.add(new PolygonLayer(source,
    fillColor("rgba(0, 200, 200, 0.5)")
), "labels");

//Create and add a line layer to render the outline of the polygon.
map.layers.add(new LineLayer(source,
    strokeColor("red"),
    strokeWidth(2f)
));
```

::: zone-end

::: zone pivot="programming-language-kotlin"

```kotlin
//Create a data source and add it to the map.
val source = DataSource()
map.sources.add(source)

//Create a rectangular polygon.
source.add(
    Polygon.fromLngLats(
        Arrays.asList(
            Arrays.asList(
                Point.fromLngLat(-73.98235, 40.76799),
                Point.fromLngLat(-73.95785, 40.80044),
                Point.fromLngLat(-73.94928, 40.79680),
                Point.fromLngLat(-73.97317, 40.76437),
                Point.fromLngLat(-73.98235, 40.76799)
            )
        )
    )
)

//Create and add a polygon layer to render the polygon on the map, below the label layer.
map.layers.add(
    PolygonLayer(
        source,
        fillColor("rgba(0, 200, 200, 0.5)")
    ), "labels"
)

//Create and add a line layer to render the outline of the polygon.
map.layers.add(
    LineLayer(
        source,
        strokeColor("red"),
        strokeWidth(2f)
    )
)
```

::: zone-end

A imagem que se segue mostra o código acima, tornando um polígono com o seu contorno renderizado usando uma camada de linha.

![Polígono com a sua área de enchimento e esboço renderizado](media/how-to-add-shapes-to-android-map/android-polygon-and-line-layer.png)

> [!TIP]
> Ao delinear um polígono com uma camada de linha, certifique-se de fechar todos os anéis em polígonos de modo a que cada matriz de pontos tenha o mesmo ponto de partida e final. Se isto não for feito, a camada de linha pode não ligar o último ponto do polígono ao primeiro ponto.

## <a name="fill-a-polygon-with-a-pattern"></a>Encha um polígono com um padrão

Além de encher um polígono com uma cor, pode usar um padrão de imagem para preencher o polígono. Carregue um padrão de imagem nos recursos de imagem dos mapas e, em seguida, refira esta imagem com a `fillPattern` opção da camada de polígono.

::: zone pivot="programming-language-java-android"

```java
//Load an image pattern into the map image sprite.
map.images.add("fill-checker-red", R.drawable.fill_checker_red);

//Create a data source and add it to the map.
DataSource source = new DataSource();
map.sources.add(source);

//Create a polygon.
source.add(Polygon.fromLngLats(
    Arrays.asList(
        Arrays.asList(
            Point.fromLngLat(-50, -20),
            Point.fromLngLat(0, 40),
            Point.fromLngLat(50, -20),
            Point.fromLngLat(-50, -20)
        )
    )
));

//Create and add a polygon layer to render the polygon on the map, below the label layer.
map.layers.add(new PolygonLayer(source,
        fillPattern("fill-checker-red"),
        fillOpacity(0.5f)
), "labels");
```

::: zone-end

::: zone pivot="programming-language-kotlin"

```kotlin
//Load an image pattern into the map image sprite.
map.images.add("fill-checker-red", R.drawable.fill_checker_red)

//Create a data source and add it to the map.
val source = DataSource()
map.sources.add(source)

//Create a polygon.
source.add(
    Polygon.fromLngLats(
        Arrays.asList(
            Arrays.asList(
                Point.fromLngLat(-50, -20),
                Point.fromLngLat(0, 40),
                Point.fromLngLat(50, -20),
                Point.fromLngLat(-50, -20)
            )
        )
    )
)

//Create and add a polygon layer to render the polygon on the map, below the label layer.
map.layers.add(
    PolygonLayer(
        source,
        fillPattern("fill-checker-red"),
        fillOpacity(0.5f)
    ), "labels"
)
```

::: zone-end

Para esta amostra, a imagem a seguir foi carregada na pasta desaudante da aplicação.

| ![Imagem de ícone de seta roxa](media/how-to-add-shapes-to-android-map/fill-checker-red.png)|
|:-----------------------------------------------------------------------:|
| fill_checker_red.png                                                    |

Segue-se uma imagem do código acima, tornando um polígono com um padrão de preenchimento no mapa.

![Polígono com um padrão de preenchimento renderizado no mapa](media/how-to-add-shapes-to-android-map/android-polygon-pattern.jpg)

## <a name="next-steps"></a>Passos seguintes

Consulte os seguintes artigos para obter mais amostras de código para adicionar aos seus mapas:

> [!div class="nextstepaction"]
> [Criar uma origem de dados](create-data-source-android-sdk.md)

> [!div class="nextstepaction"]
> [Utilizar expressões de estilo com base em dados](data-driven-style-expressions-android-sdk.md)

> [!div class="nextstepaction"]
> [Adicionar uma camada de linhas](android-map-add-line-layer.md)

> [!div class="nextstepaction"]
> [Adicionar uma camada de extrusão de polígonos](map-extruded-polygon-android.md)
