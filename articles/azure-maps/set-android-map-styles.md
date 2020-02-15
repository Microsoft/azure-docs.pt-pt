---
title: Detete um estilo de mapa usando o Azure Maps Android SDK. Microsoft Azure Maps
description: Neste artigo, você vai aprender sobre as funcionalidades relacionadas com o estilo Do Microsoft Azure Maps para o Android SDK.
author: farah-alyasari
ms.author: v-faalya
ms.date: 04/26/2019
ms.topic: conceptual
ms.service: azure-maps
services: azure-maps
manager: philmea
ms.openlocfilehash: 96564a89a2b64203eef913b0d8300f0dafa332c5
ms.sourcegitcommit: 2823677304c10763c21bcb047df90f86339e476a
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 02/14/2020
ms.locfileid: "77209584"
---
# <a name="set-map-style-using-azure-maps-android-sdk"></a>Definir o estilo do mapa usando O Android SDK do Azure Maps

Este artigo mostra-lhe duas formas de definir os estilos de mapa usando o Azure Maps Android SDK. O Azure Maps tem seis estilos de mapas diferentes para escolher. Para obter mais informações sobre estilos de mapas suportados, consulte [os estilos de mapas suportados no Azure Maps.](./supported-map-styles.md)


## <a name="prerequisites"></a>Pré-requisitos

Para completar o processo neste artigo, é necessário instalar o [Azure Maps Android SDK](https://docs.microsoft.com/azure/azure-maps/how-to-use-android-map-control-library) para carregar um mapa.


## <a name="set-map-style-in-the-layout"></a>Definir o estilo do mapa no layout

Pode definir um estilo de mapa no ficheiro de layout para a sua aula de atividade. Editar **res > layout > activity_main.xml,** para que se pareça com o abaixo:

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

O atributo `mapcontrol_style` acima define o estilo do mapa para **grayscale_dark**. 

<center>

![](./media/set-android-map-styles/grayscale-dark.png)</center> de grayscale_dark de estilo

## <a name="set-map-style-in-the-activity-class"></a>Definir o estilo do mapa na classe de atividade

O estilo do mapa pode ser definido na classe de atividade. Copie o seguinte código no método **onCreate()** da sua classe `MainActivity.java`. Este código definirá o estilo do mapa para **satellite_road_labels**.

```Java
mapControl.onReady(map -> {
    //Set the camera of the map.
    map.setCamera(center(47.64, -122.33), zoom(14));

    //Set the style of the map.
    map.setStyle(style(MapStyle.SATELLITE));
});
```

<center>

![rótulos de estrada-satélite](./media/set-android-map-styles/satellite-road-labels.png)</center>