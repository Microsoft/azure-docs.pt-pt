---
title: Desa parte para o estilo do mapa do Android Microsoft Azure Maps
description: Aprenda duas formas de definir o estilo de um mapa. Veja como usar o Azure Maps Android SDK no ficheiro layout ou na classe de atividade para ajustar o estilo.
author: rbrundritt
ms.author: richbrun
ms.date: 04/26/2019
ms.topic: conceptual
ms.service: azure-maps
services: azure-maps
manager: cpendle
ms.openlocfilehash: 1cce355c8ffbcd4704bd32b0e4d1739c77c2b623
ms.sourcegitcommit: 66b0caafd915544f1c658c131eaf4695daba74c8
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 12/18/2020
ms.locfileid: "97678463"
---
# <a name="set-map-style-android-sdk"></a>Definir estilo de mapa (Android SDK)

Este artigo mostra-lhe duas maneiras de definir estilos de mapa usando o Azure Maps Android SDK. O Azure Maps tem seis diferentes estilos de mapas para escolher. Para obter mais informações sobre estilos de mapa suportados, consulte [os estilos de mapa suportados no Azure Maps.](supported-map-styles.md)

## <a name="prerequisites"></a>Pré-requisitos

Certifique-se de completar os passos no [Quickstart: Criar um documento de aplicação Android.](quick-android-map.md)

## <a name="set-map-style-in-the-layout"></a>Definir estilo de mapa no layout

Pode definir um estilo de mapa no ficheiro de layout para a sua classe de atividade ao adicionar o controlo do mapa. O código a seguir define a localização central, o nível de zoom e o estilo do mapa.

```XML
<com.microsoft.azure.maps.mapcontrol.MapControl
    android:id="@+id/mapcontrol"
    android:layout_width="match_parent"
    android:layout_height="match_parent"
    app:mapcontrol_centerLat="47.602806"
    app:mapcontrol_centerLng="-122.329330"
    app:mapcontrol_zoom="12"
    app:mapcontrol_style="grayscale_dark"
    />
```

A imagem a seguir mostra o código acima mostrando um roteiro com o estilo escuro em tons de cinza.

![Mapa com estilo de roteiro escuro em tons de cinza](media/set-android-map-styles/android-grayscale-dark.png)

## <a name="set-map-style-in-code"></a>Definir estilo de mapa no código

O estilo do mapa pode ser definido programáticamente em código usando o `setStyle` método do mapa. O código a seguir define a localização central e o nível de zoom usando o método dos mapas `setCamera` e o estilo do mapa para `SATELLITE_ROAD_LABELS` .

```java
mapControl.onReady(map -> {

    //Set the camera of the map.
    map.setCamera(center(Point.fromLngLat(-122.33, 47.64)), zoom(14));

    //Set the style of the map.
    map.setStyle(style(MapStyle.SATELLITE_ROAD_LABELS));
});
```

A imagem que se segue mostra o código acima mostrando um mapa com o estilo de etiquetas de estrada por satélite.

![Mapa com estilo de etiquetas de estrada de satélite](media/set-android-map-styles/android-satellite-road-labels.png)

## <a name="setting-the-map-camera"></a>Definição da câmera do mapa

A câmara do mapa controla a parte do mapa que é exibida no mapa. A câmara pode estar no layout do nosso programático código. Ao defini-lo em código, existem dois métodos principais para definir a posição do mapa; usando o centro e o zoom, ou passando em uma caixa de delimitação. O código que se segue mostra como definir todas as opções opcionais de câmara ao utilizar `center` e `zoom` .

```java
//Set the camera of the map using center and zoom.
map.setCamera(
    center(Point.fromLngLat(-122.33, 47.64)), 

    //The zoom level. Typically a value between 0 and 22.
    zoom(14),

    //The amount of tilt in degrees the map where 0 is looking straight down.
    pitch(45),

    //Direction the top of the map is pointing in degrees. 0 = North, 90 = East, 180 = South, 270 = West
    bearing(90),

    //The minimum zoom level the map will zoom-out to when animating from one location to another on the map.
    minZoom(10),
    
    //The maximium zoom level the map will zoom-in to when animating from one location to another on the map.
    maxZoom(14)
);
```

Muitas vezes é desejável focar o mapa sobre um conjunto de dados. Uma caixa de delimitação pode ser calculada a partir de funcionalidades usando o `MapMath.fromData` método e pode ser passada para a `bounds` opção da câmara do mapa. Ao definir uma vista de mapa com base numa caixa de delimitação, é frequentemente útil especificar um `padding` valor para explicar o tamanho do pixel de pontos sendo renderizados como bolhas ou símbolos. O código que se segue mostra como definir todas as opções de câmara opcionais quando se utiliza uma caixa de limitação para definir a posição da câmara.

```java
//Set the camera of the map using a bounding box.
map.setCamera(
    //The area to focus the map on.
    bounds(BoundingBox.fromLngLats(
        //West
        -122.4594,

        //South
        47.4333,
        
        //East
        -122.21866,
        
        //North
        47.75758
    )),

    //Amount of pixel buffer around the bounding box to provide extra space around the bounding box.
    padding(20),

    //The maximium zoom level the map will zoom-in to when animating from one location to another on the map.
    maxZoom(14)
);
```

Note que a relação de aspeto de uma caixa de delimitação pode não ser a mesma que a relação de aspeto do mapa, como tal o mapa irá muitas vezes mostrar a área da caixa de delimitação completa, mas muitas vezes só será apertado vertical ou horizontalmente.

## <a name="next-steps"></a>Passos seguintes

Consulte os seguintes artigos para obter mais amostras de código para adicionar aos seus mapas:

> [!div class="nextstepaction"]
> [Adicionar uma camada de símbolo](how-to-add-symbol-to-android-map.md)

> [!div class="nextstepaction"]
> [Adicionar uma camada de bolha](map-add-bubble-layer-android.md)
