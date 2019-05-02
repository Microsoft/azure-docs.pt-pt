---
title: Adicionar uma camada de símbolo para o Android mapas do Azure Maps | Documentos da Microsoft
description: Como adicionar os símbolos para um mapa com o SDK Android do Azure Maps
author: walsehgal
ms.author: v-musehg
ms.date: 04/26/2019
ms.topic: conceptual
ms.service: azure-maps
services: azure-maps
manager: philmea
ms.openlocfilehash: add6e23d023753e217c102dc946837a71a64c781
ms.sourcegitcommit: e7d4881105ef17e6f10e8e11043a31262cfcf3b7
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/29/2019
ms.locfileid: "64871081"
---
# <a name="add-a-symbol-layer-to-a-map-using-azure-maps-android-sdk"></a>Adicionar uma camada de símbolo para um mapa com o SDK Android do Azure Maps

Este artigo mostra-lhe como processar dados de ponto de uma origem de dados como uma camada de símbolo num mapa com o SDK Android do Azure Maps.

## <a name="prerequisites"></a>Pré-requisitos

Para seguir completamente os passos neste artigo, tem de instalar [SDK Android do Azure Maps](https://docs.microsoft.com/azure/azure-maps/how-to-use-android-map-control-library) para carregar um mapa.

## <a name="add-a-symbol-layer"></a>Adicionar uma camada de símbolo

Para adicionar um marcador no mapa com a camada de símbolo, siga os passos abaixo:

1. Editar **res** > **layout** > **ctivity_main** para que ele se parece com o seguinte XML:
    
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
            app:mapcontrol_centerLat="47.64"
            app:mapcontrol_centerLng="-122.33"
            app:mapcontrol_zoom="12"
            />

    </FrameLayout>
    ```

2. Copie o seguinte trecho de código para o **onCreate()** método de sua `MainActivity.java` classe.

    ```Java
    mapControl.onReady(map -> {
    
        //Create a data source and add it to the map.
        DataSource dataSource = new DataSource();
        map.sources.add(dataSource);
    
        //Create a point feature and add it to the data source.
        dataSource.add(Feature.fromGeometry(Point.fromLngLat(-122.33, 47.64)));
    
        //Add a custom image icon to the map resources.
        map.images.add("my-icon", R.drawable.mapcontrol_marker_red);
    
        //Create a symbol layer and add it to the map.
        map.layers.add(new SymbolLayer(dataSource,
            iconImage("my-icon")));
        });
    
    ```
    
    O fragmento de código acima obtém primeiro a utilizar uma instância do Azure Maps mapa controle os **onReady()** método de retorno de chamada. Em seguida, cria uma origem de dados objeto utilizando a **origem de dados** de classe e adiciona-o ao mapa. Em seguida, adiciona um **funcionalidade** que contém uma geometria de ponto a ele. Uma imagem de marcador vermelho, em seguida, está definida como ícone para o símbolo. R **camada de símbolo** usa ou ícones de texto para processar dados com base no ponto encapsulados na origem de dados, como o símbolo no mapa. Uma camada de símbolo, em seguida, é criada e a origem de dados é passada para ele ser processado e, em seguida, é adicionada ao camadas do mapa.
    
    Depois de adicionar o fragmento de código acima, seu `MainActivity.java` deve ter um aspeto semelhante ao mostrado abaixo:
    
    ```Java
    package com.example.myapplication;
    
    import android.app.Activity;
    import android.os.Bundle;
    import com.mapbox.geojson.Feature;
    import com.mapbox.geojson.Point;
    import com.microsoft.azure.maps.mapcontrol.AzureMaps;
    import com.microsoft.azure.maps.mapcontrol.MapControl;
    import com.microsoft.azure.maps.mapcontrol.layer.SymbolLayer;
    import com.microsoft.azure.maps.mapcontrol.source.DataSource;
    import static com.microsoft.azure.maps.mapcontrol.options.SymbolLayerOptions.iconImage;
    public class MainActivity extends AppCompatActivity {
        
        static{
                AzureMaps.setSubscriptionKey("<Your Azure Maps subscription key>");
            }
    
        MapControl mapControl;
        @Override
        protected void onCreate(Bundle savedInstanceState) {
            super.onCreate(savedInstanceState);
            setContentView(R.layout.activity_main);
    
            mapControl = findViewById(R.id.mapcontrol);
    
            mapControl.onCreate(savedInstanceState);
    
            mapControl.onReady(map -> {
    
                //Create a data source and add it to the map.
                DataSource dataSource = new DataSource();
                map.sources.add(dataSource);
            
                //Create a point feature and add it to the data source.
                dataSource.add(Feature.fromGeometry(Point.fromLngLat(-122.33, 47.64)));
            
                //Add a custom image icon to the map resources.
                map.images.add("my-icon", R.drawable.mapcontrol_marker_red);
            
                //Create a symbol layer and add it to the map.
                map.layers.add(new SymbolLayer(dataSource,
                    iconImage("my-icon")));
            });
        }
    
        @Override
        public void onStart() {
            super.onStart();
            mapControl.onStart();
        }
    
        @Override
        public void onResume() {
            super.onResume();
            mapControl.onResume();
        }
    
        @Override
        public void onPause() {
            super.onPause();
            mapControl.onPause();
        }
    
        @Override
        public void onStop() {
            super.onStop();
            mapControl.onStop();
        }
    
        @Override
        public void onLowMemory() {
            super.onLowMemory();
            mapControl.onLowMemory();
        }
    
        @Override
        protected void onDestroy() {
            super.onDestroy();
            mapControl.onDestroy();
        }
    
        @Override
        protected void onSaveInstanceState(Bundle outState) {
            super.onSaveInstanceState(outState);
            mapControl.onSaveInstanceState(outState);
        }
    }
    ```
    
Neste momento, se executar a aplicação deverá ver um marcador no mapa, conforme mostrado aqui:

<center>

![Pin de mapa de Android](./media/how-to-add-symbol-to-android-map/android-map-pin.png)</center>


## <a name="next-steps"></a>Passos Seguintes

Para adicionar mais coisas para seu mapa, consulte:

> [!div class="nextstepaction"]
> [Adicionar formas para um mapa de Android](https://docs.microsoft.com/azure/azure-maps/how-to-add-shapes-to-android-map)