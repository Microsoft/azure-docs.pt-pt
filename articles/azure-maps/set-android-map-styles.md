---
title: Mapear as funcionalidades de estilo no Azure Maps | Documentos da Microsoft
description: Saiba mais sobre o Azure Maps estilo relacionado funcionalidades para o Android SDK.
author: walsehgal
ms.author: v-musehg
ms.date: 04/26/2019
ms.topic: conceptual
ms.service: azure-maps
services: azure-maps
manager: philmea
ms.openlocfilehash: 3c8c5d4bae16d8e15c8f2c5b1cc8e00eb14e4ce3
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 06/13/2019
ms.locfileid: "64870976"
---
# <a name="set-map-style-using-azure-maps-android-sdk"></a>Definir o estilo de mapa com o SDK Android do Azure Maps

Este artigo mostra-lhe duas formas de definir estilos de mapa com o SDK Android do Azure Maps. Mapas do Azure tem seis mapas diferentes estilos à sua escolha. Para obter mais informações sobre os estilos de mapa suportados, consulte [suportado estilos de mapa do Azure Maps](./supported-map-styles.md).


## <a name="prerequisites"></a>Pré-requisitos

Para concluir o processo neste artigo, tem de instalar [SDK Android do Azure Maps](https://docs.microsoft.com/azure/azure-maps/how-to-use-android-map-control-library) para carregar um mapa.


## <a name="set-map-style-in-the-layout"></a>Definir o estilo de mapa no layout

Pode definir um estilo de mapa no arquivo de layout para sua classe de atividade. Editar **res > layout > ctivity_main**, por isso, ele será semelhante à mostrada abaixo:

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

O `mapcontrol_style` acima do atributo define o estilo de mapa **grayscale_dark**. 

<center>

![style-grayscale_dark](./media/set-android-map-styles/grayscale-dark.png)</center>

## <a name="set-map-style-in-the-activity-class"></a>Definir o estilo de mapa da classe de atividade

Estilo de mapa pode ser definido na classe activity. Copie o seguinte trecho de código para o **onCreate()** método de sua `MainActivity.java` classe. Isto irá definir o estilo de mapa como **satellite_road_labels**.

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