---
title: Adicione uma camada de bolha aos mapas Android | Microsoft Azure Maps
description: Aprenda a renderizar pontos em mapas como círculos com tamanhos fixos. Veja como usar o Azure Maps Android SDK para adicionar e personalizar camadas de bolha para este fim.
author: rbrundritt
ms.author: richbrun
ms.date: 2/26/2021
ms.topic: conceptual
ms.service: azure-maps
services: azure-maps
manager: cpendle
zone_pivot_groups: azure-maps-android
ms.openlocfilehash: f3c175f30c5c0e6206f4fee274e0f3f000e55a74
ms.sourcegitcommit: 4b7a53cca4197db8166874831b9f93f716e38e30
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/04/2021
ms.locfileid: "102100173"
---
# <a name="add-a-bubble-layer-to-a-map-android-sdk"></a>Adicione uma camada de bolha a um mapa (Android SDK)

Este artigo mostra-lhe como fazer dados pontuais a partir de uma fonte de dados como uma camada de bolha num mapa. Camadas de bolhas tornam os pontos como círculos no mapa com um raio de pixel fixo.

> [!TIP]
> Camadas de bolha por defeito tornarão as coordenadas de todas as geometrias numa fonte de dados. Para limitar a camada de modo a que apenas torne as características de geometria do ponto, deite `filter` a opção da camada para `eq(geometryType(), "Point")` . Se também pretender incluir funcionalidades MultiPoint, deite `filter` a opção da camada para `any(eq(geometryType(), "Point"), eq(geometryType(), "MultiPoint"))` .

## <a name="prerequisites"></a>Pré-requisitos

Certifique-se de completar os passos no [Quickstart: Criar um documento de aplicação Android.](quick-android-map.md) Os blocos de código deste artigo podem ser inseridos no manipulador de eventos de `onReady` mapas.

## <a name="add-a-bubble-layer"></a>Adicionar uma camada de bolha

O código seguinte carrega uma série de pontos numa fonte de dados. Em seguida, liga os pontos de dados a uma camada de bolha. A camada de bolha torna o raio de cada bolha com cinco pixéis e uma cor de preenchimento de branco. E, uma cor de traçado de azul, e uma largura de curso de seis píxeis.

::: zone pivot="programming-language-java-android"

```java
//Create a data source and add it to the map.
DataSource source = new DataSource();
map.sources.add(source);

//Create point locations.
Point[] points = new Point[] {
    Point.fromLngLat(-73.985708, 40.75773),
    Point.fromLngLat(-73.985600, 40.76542),
    Point.fromLngLat(-73.985550, 40.77900),
    Point.fromLngLat(-73.975550, 40.74859),
    Point.fromLngLat(-73.968900, 40.78859)
};

//Add multiple points to the data source.
source.add(points);

//Create a bubble layer to render the filled in area of the circle, and add it to the map.
BubbleLayer layer = new BubbleLayer(source, 
    bubbleRadius(5f),
    bubbleColor("white"),
    bubbleStrokeColor("#4288f7"),
    bubbleStrokeWidth(6f)
);

map.layers.add(layer);
```

::: zone-end

::: zone pivot="programming-language-kotlin"

```kotlin
//Create a data source and add it to the map.
val source = DataSource()
map.sources.add(source)

//Create point locations.
val points: Array<Point> = arrayOf<Point>(
    Point.fromLngLat(-73.985708, 40.75773),
    Point.fromLngLat(-73.985600, 40.76542),
    Point.fromLngLat(-73.985550, 40.77900),
    Point.fromLngLat(-73.975550, 40.74859),
    Point.fromLngLat(-73.968900, 40.78859)
)

//Add multiple points to the data source.
source.add(points)

//Create a bubble layer to render the filled in area of the circle, and add it to the map.
val layer = BubbleLayer(
    source,
    bubbleRadius(5f),
    bubbleColor("white"),
    bubbleStrokeColor("#4288f7"),
    bubbleStrokeWidth(6f)
)

map.layers.add(layer)
```

::: zone-end

A imagem que se segue mostra o código acima, torna os pontos numa camada de bolha.

![Mapa com pontos renderizados usando camada de bolha](media/map-add-bubble-layer-android/android-bubble-layer.png)

## <a name="show-labels-with-a-bubble-layer"></a>Mostrar rótulos com uma camada de bolha

Este código mostra como usar uma camada de bolha para tornar um ponto no mapa. E, como usar uma camada de símbolo para renderizar uma etiqueta. Para ocultar o ícone da camada de símbolo, desagreda `iconImage` a opção para `"none"` .

::: zone pivot="programming-language-java-android"

```java
//Create a data source and add it to the map.
DataSource source = new DataSource();
map.sources.add(source);

//Add a data point to the map.
source.add(Point.fromLngLat(-122.336641,47.627631));

//Add a bubble layer.
map.layers.add(new BubbleLayer(source,
    bubbleRadius(5f),
    bubbleColor("white"),
    bubbleStrokeColor("#4288f7"),
    bubbleStrokeWidth(6f)
));

//Add a symbol layer to display text, hide the icon image.
map.layers.add(new SymbolLayer(source,
    //Hide the icon image.
    iconImage("none"),
    textField("Museum of History & Industry (MOHAI)"),
    textColor("#005995"),
    textOffset(new Float[]{0f, -2.2f})
));
```

::: zone-end

::: zone pivot="programming-language-kotlin"

```kotlin
//Create a data source and add it to the map.
val source = DataSource()
map.sources.add(source)

//Add a data point to the map.
source.add(Point.fromLngLat(-122.336641, 47.627631))

//Add a bubble layer.
map.layers.add(
    BubbleLayer(
        source,
        bubbleRadius(5f),
        bubbleColor("white"),
        bubbleStrokeColor("#4288f7"),
        bubbleStrokeWidth(6f)
    )
)

//Add a symbol layer to display text, hide the icon image.
map.layers.add(
    SymbolLayer(
        source,  //Hide the icon image.
        iconImage("none"),
        textField("Museum of History & Industry (MOHAI)"),
        textColor("#005995"),
        textOffset(arrayOf(0f, -2.2f))
    )
)
```

::: zone-end

A imagem que se segue mostra o código acima, mostrando um ponto numa camada de bolha e uma etiqueta de texto para o ponto com uma camada de símbolo.

![Mapa com ponto renderizado usando uma camada de bolha e um rótulo de texto com camada de símbolo](media/map-add-bubble-layer-android/android-bubble-symbol-layer.png)

## <a name="next-steps"></a>Passos seguintes

Consulte os seguintes artigos para obter mais amostras de código para adicionar aos seus mapas:

> [!div class="nextstepaction"]
> [Criar uma origem de dados](create-data-source-android-sdk.md)

> [!div class="nextstepaction"]
> [Adicionar uma camada de símbolo](how-to-add-symbol-to-android-map.md)

> [!div class="nextstepaction"]
> [Utilizar expressões de estilo com base em dados](data-driven-style-expressions-android-sdk.md)

> [!div class="nextstepaction"]
> [Apresentar informações da funcionalidade](display-feature-information-android.md)
