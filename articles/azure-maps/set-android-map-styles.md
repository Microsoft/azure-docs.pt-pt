---
title: Desconfiem de um estilo de mapa usando o Azure Maps Android SDK. Microsoft Azure Maps
description: Neste artigo, você vai aprender sobre as funcionalidades relacionadas com o estilo do Microsoft Azure Maps para o Android SDK.
author: anastasia-ms
ms.author: v-stharr
ms.date: 04/26/2019
ms.topic: conceptual
ms.service: azure-maps
services: azure-maps
manager: philmea
ms.openlocfilehash: 81f89a4b5aa758d506ecdb9cf7b4f510e333ef25
ms.sourcegitcommit: 0e8a4671aa3f5a9a54231fea48bcfb432a1e528c
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 07/24/2020
ms.locfileid: "87131264"
---
# <a name="set-map-style-using-azure-maps-android-sdk"></a>Definir estilo de mapa usando Azure Maps Android SDK

Este artigo mostra-lhe duas maneiras de definir estilos de mapa usando o Azure Maps Android SDK. O Azure Maps tem seis diferentes estilos de mapas para escolher. Para obter mais informações sobre estilos de mapa suportados, consulte [os estilos de mapa suportados no Azure Maps.](./supported-map-styles.md)


## <a name="prerequisites"></a>Pré-requisitos

Para completar o processo neste artigo, precisa instalar [o Azure Maps Android SDK](https://docs.microsoft.com/azure/azure-maps/how-to-use-android-map-control-library) para carregar um mapa.


## <a name="set-map-style-in-the-layout"></a>Definir estilo de mapa no layout

Pode definir um estilo de mapa no ficheiro de layout para a sua aula de atividade. Editar **res > layout > activity_main.xml**, por isso parece o de baixo:

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

O `mapcontrol_style` atributo acima define o estilo do mapa para **grayscale_dark**. 

<center>

![estilo-grayscale_dark](./media/set-android-map-styles/grayscale-dark.png)</center>

## <a name="set-map-style-in-the-activity-class"></a>Definir estilo de mapa na classe de atividade

O estilo do mapa pode ser definido na classe de atividade. Copie o seguinte corte de código no método **onCreate()** da sua `MainActivity.java` classe. Este código definirá o estilo do mapa para **satellite_road_labels**.

```Java
mapControl.onReady(map -> {
    //Set the camera of the map.
    map.setCamera(center(47.64, -122.33), zoom(14));

    //Set the style of the map.
    map.setStyle(style(MapStyle.SATELLITE));
});
```

<center>

![rótulos de estrada de satélite de estilo](./media/set-android-map-styles/satellite-road-labels.png)</center>