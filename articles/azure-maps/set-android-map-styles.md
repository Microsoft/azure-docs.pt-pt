---
title: Definir um estilo de mapa no Azure Maps usando SDK do Android
titleSuffix: Azure Maps
description: Saiba mais sobre as funcionalidades relacionadas ao estilo do Azure Maps para SDK do Android.
author: walsehgal
ms.author: v-musehg
ms.date: 04/26/2019
ms.topic: conceptual
ms.service: azure-maps
services: azure-maps
manager: philmea
ms.openlocfilehash: 1a898508e5c99f6cb8be46605c156106b47c08f3
ms.sourcegitcommit: ce4a99b493f8cf2d2fd4e29d9ba92f5f942a754c
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 12/28/2019
ms.locfileid: "75528101"
---
# <a name="set-map-style-using-azure-maps-android-sdk"></a>Definir o estilo de mapa usando o Azure Maps SDK do Android

Este artigo mostra duas maneiras de definir estilos de mapa usando o SDK do Android do Azure Maps. O mapas do Azure tem seis estilos de mapas diferentes para escolher. Para obter mais informações sobre os estilos de mapa com suporte, consulte [estilos de mapa com suporte no mapas do Azure](./supported-map-styles.md).


## <a name="prerequisites"></a>Pré-requisitos

Para concluir o processo neste artigo, você precisa instalar o [Azure Maps SDK do Android](https://docs.microsoft.com/azure/azure-maps/how-to-use-android-map-control-library) para carregar um mapa.


## <a name="set-map-style-in-the-layout"></a>Definir o estilo de mapa no layout

Você pode definir um estilo de mapa no arquivo de layout para sua classe de atividade. Edite o **layout de > res > activity_main. xml**, portanto, é semelhante ao seguinte:

```XML
<?xml version="1.0" encoding="utf-8"?>
<FrameLayout
    xmlns:android="http://schemas.android.com/apk/res/android"
    xmlns:app="http://schemas.android.com/apk/res-auto"
    android:layout_width="match_parent"
    android:layout_height="match_parent"
    >

    <com.microsoft.azure.maps.mapcontrol.MapControl
        android:id="@+id/mapcontrol"
        android:layout_width="match_parent"
        android:layout_height="match_parent"
        app:mapcontrol_centerLat="47.602806"
        app:mapcontrol_centerLng="-122.329330"
        app:mapcontrol_zoom="12"
        app:mapcontrol_style="grayscale_dark"
        />

</FrameLayout>
```

O atributo `mapcontrol_style` acima define o estilo de mapa como **grayscale_dark**. 

<center>

![style-grayscale_dark](./media/set-android-map-styles/grayscale-dark.png)</center>

## <a name="set-map-style-in-the-activity-class"></a>Definir o estilo de mapa na classe de atividade

O estilo de mapa pode ser definido na classe de atividade. Copie o trecho de código a seguir para o método **OnCreate ()** da sua classe `MainActivity.java`. Isso definirá o estilo do mapa como **satellite_road_labels**.

```Java
mapControl.onReady(map -> {
    //Set the camera of the map.
    map.setCamera(center(47.64, -122.33), zoom(14));

    //Set the style of the map.
    map.setStyle(style(MapStyle.SATELLITE));
});
```

<center>

![style-satellite-road-labels](./media/set-android-map-styles/satellite-road-labels.png)</center>