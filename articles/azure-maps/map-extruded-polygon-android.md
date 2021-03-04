---
title: Adicione uma camada de extrusão de polígono a um mapa Android | Microsoft Azure Maps
description: Como adicionar uma camada de extrusão de polígono ao Microsoft Azure Maps Android SDK.
author: rbrundritt
ms.author: richbrun
ms.date: 02/19/2021
ms.topic: conceptual
ms.service: azure-maps
services: azure-maps
manager: cpendle
ms.openlocfilehash: b62c2540dc8cf2c7d4f67d465b2d464cbc0c3091
ms.sourcegitcommit: f3ec73fb5f8de72fe483995bd4bbad9b74a9cc9f
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/04/2021
ms.locfileid: "102055056"
---
# <a name="add-a-polygon-extrusion-layer-to-the-map-android-sdk"></a>Adicione uma camada de extrusão de polígono ao mapa (Android SDK)

Este artigo mostra-lhe como usar a camada de extrusão do polígono para tornar áreas `Polygon` e `MultiPolygon` características geometrias como formas extrudidas.

## <a name="use-a-polygon-extrusion-layer"></a>Use uma camada de extrusão de polígono

Ligue a camada de extrusão do polígono a uma fonte de dados. Depois, carreguei-o no mapa. A camada de extrusão do polígono torna as áreas de um `Polygon` e `MultiPolygon` apresenta-se como formas extrudidas. As `height` propriedades e propriedades da camada de `base` extrusão do polígono definem a distância de base do solo e da altura da forma extrudida em **metros**. O código que se segue mostra como criar um polígono, adicioná-lo a uma fonte de dados, e torná-lo usando a classe de camada de extrusão do Polygon.

> [!Note]
> O `base` valor definido na camada de extrusão do polígono deve ser inferior ou igual ao da `height` .

```java
//Create a data source and add it to the map.
DataSource source = new DataSource();
map.sources.add(source);

//Create a polygon.
source.add(Polygon.fromLngLats(
    Arrays.asList(
        Arrays.asList(
            Point.fromLngLat(-73.95838379859924, 40.80027995478159),
            Point.fromLngLat(-73.98154735565186, 40.76845986171129),
            Point.fromLngLat(-73.98124694824219, 40.767761062136955),
            Point.fromLngLat(-73.97361874580382, 40.76461637311633),
            Point.fromLngLat(-73.97306084632874, 40.76512830937617),
            Point.fromLngLat(-73.97259950637817, 40.76490890860481),
            Point.fromLngLat(-73.9494466781616, 40.79658450499243),
            Point.fromLngLat(-73.94966125488281, 40.79708807289436),
            Point.fromLngLat(-73.95781517028809, 40.80052360358227),
            Point.fromLngLat(-73.95838379859924, 40.80027995478159)
        )
    )
));

//Create and add a polygon extrusion layer to the map below the labels so that they are still readable.
PolygonExtrusionLayer layer = new PolygonExtrusionLayer(source,
    fillColor("#fc0303"),
    fillOpacity(0.7f),
    height(500f)
);

//Create and add a polygon extrusion layer to the map below the labels so that they are still readable.
map.layers.add(layer, "labels");
```

A imagem que se segue mostra o código acima a rending um polígono esticado verticalmente usando uma camada de extrusão de polígono.

![Mapa com polígono esticado verticalmente usando uma camada de extrusão de polígono](media/map-extruded-polygon-android/polygon-extrusion-layer.jpg)

## <a name="add-data-driven-polygons"></a>Adicionar polígonos orientados a dados

Um mapa de choropleth pode ser renderizado usando a camada de extrusão do polígono. Definir as `height` propriedades e propriedades da camada de `fillColor` extrusão para a medição da variável estatística nas `Polygon` `MultiPolygon` geometrias e características. A seguinte amostra de código mostra um mapa extrudido dos Estados Unidos com base na medição da densidade populacional por estado.

```java
//Create a data source and add it to the map.
DataSource source = new DataSource();
map.sources.add(source);

//Import the geojson data and add it to the data source.
Utils.importData("https://azuremapscodesamples.azurewebsites.net/Common/data/geojson/US_States_Population_Density.json", this,  (String result) -> {
    //Parse the data as a GeoJSON Feature Collection.
    FeatureCollection fc = FeatureCollection.fromJson(result);

    //Add the feature collection to the data source.
    source.add(fc);
});

//Create and add a polygon extrusion layer to the map below the labels so that they are still readable.
PolygonExtrusionLayer layer = new PolygonExtrusionLayer(source,
    fillOpacity(0.7f),
    fillColor(
        step(
            get("density"),
            literal("rgba(0, 255, 128, 1)"),
            stop(10, "rgba(9, 224, 118, 1)"),
            stop(20, "rgba(11, 191, 103, 1)"),
            stop(50, "rgba(247, 227, 5, 1)"),
            stop(100, "rgba(247, 199, 7, 1)"),
            stop(200, "rgba(247, 130, 5, 1)"),
            stop(500, "rgba(247, 94, 5, 1)"),
            stop(1000, "rgba(247, 37, 5, 1)")
        )
    ),
    height(
        interpolate(
            linear(),
            get("density"),
            stop(0, 100),
            stop(1200, 960000)
        )
    )
);

//Create and add a polygon extrusion layer to the map below the labels so that they are still readable.
map.layers.add(layer, "labels");
```

A imagem que se segue mostra um mapa de coropleth de estados norte-americanos coloridos e esticados verticalmente como polígonos extrudidos com base na densidade populacional.

![Um mapa de coropleth dos estados dos EUA colorido e esticado verticalmente como polígonos extrudidos com base na densidade populacional](media/map-extruded-polygon-android/android-extruded-choropleth.jpg)

## <a name="next-steps"></a>Passos seguintes

Consulte os seguintes artigos para obter mais amostras de código para adicionar aos seus mapas:

> [!div class="nextstepaction"]
> [Criar uma origem de dados](create-data-source-android-sdk.md)

> [!div class="nextstepaction"]
> [Utilizar expressões de estilo com base em dados](data-driven-style-expressions-android-sdk.md)

> [!div class="nextstepaction"]
> [Adicionar uma camada de polígonos](how-to-add-shapes-to-android-map.md)
