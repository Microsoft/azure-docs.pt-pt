---
title: Desconfiem de um estilo de mapa usando O Azure Maps Android SDK
description: Aprenda duas formas de definir o estilo de um mapa. Veja como usar o Microsoft Azure Maps Android SDK no ficheiro de layout ou na classe de atividade para ajustar o estilo.
author: anastasia-ms
ms.author: v-stharr
ms.date: 11/18/2020
ms.topic: how-to
ms.service: azure-maps
services: azure-maps
manager: philmea
ms.openlocfilehash: 8c7689fb87575ac6e150f793b43f35e8bf6adc83
ms.sourcegitcommit: 5b93010b69895f146b5afd637a42f17d780c165b
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 12/02/2020
ms.locfileid: "96532488"
---
# <a name="set-map-style-using-azure-maps-android-sdk"></a>Definir estilo de mapa usando Azure Maps Android SDK

Este artigo mostra-lhe como definir estilos de mapa usando o Azure Maps Android SDK. O Azure Maps tem seis diferentes estilos de mapas para escolher. Para obter mais informações sobre estilos de mapa suportados, consulte [os estilos de mapa suportados no Azure Maps.](./supported-map-styles.md)

## <a name="prerequisites"></a>Pré-requisitos

1. [Faça uma conta Azure Maps](quick-demo-map-app.md#create-an-azure-maps-account)
2. [Obtenha uma chave de subscrição primária,](quick-demo-map-app.md#get-the-primary-key-for-your-account)também conhecida como a chave primária ou a chave de subscrição.
3. Faça o download e instale o [Azure Maps Android SDK](./how-to-use-android-map-control-library.md).


## <a name="set-map-style-in-the-layout"></a>Definir estilo de mapa no layout

Pode definir um estilo de mapa no ficheiro de layout para a sua aula de atividade. `res > layout > activity_main.xml`Editar, assim parece o de baixo:

```XML
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

:::image type="content" source="./media/set-android-map-styles/grayscale-dark.png" border="true" alt-text="Azure Maps, imagem de mapa mostrando o estilo como grayscale_dark":::

## <a name="set-map-style-in-the-mainactivity-class"></a>Definir o estilo do mapa na classe MainActivity

O estilo do mapa também pode ser definido na classe MainActivity. Abra o `java > com.example.myapplication > MainActivity.java` ficheiro e copie o seguinte corte de código no método **onCreate().** Este código define o estilo do mapa para **satellite_road_labels**.

>[!WARNING]
>O Android Studio pode não ter importado as aulas necessárias.  Como resultado, o código terá algumas referências insolúveis. Para importar as classes necessárias, basta pairar sobre cada referência não resolvida e pressionar `Alt + Enter` (Opção + Retorno num Mac).

```Java
mapControl.onReady(map -> {

    //Set the camera of the map.
    map.setCamera(center(47.64, -122.33), zoom(14));

    //Set the style of the map.
    map.setStyle((style(SATELLITE_ROAD_LABELS)));
       
});
```

:::image type="content" source="./media/set-android-map-styles/satellite-road-labels.png" border="true" alt-text="Azure Maps, imagem de mapa mostrando o estilo como satellite_road_labels":::