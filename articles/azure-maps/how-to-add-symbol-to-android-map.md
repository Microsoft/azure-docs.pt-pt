---
title: Adicione uma camada de símbolo aos mapas Android Microsoft Azure Maps
description: Neste artigo, você aprenderá a renderizar dados de pontos num mapa adicionando-lhe uma camada de símbolo usando o Microsoft Azure Maps Android SDK.
author: philmea
ms.author: philmea
ms.date: 04/26/2019
ms.topic: conceptual
ms.service: azure-maps
services: azure-maps
manager: philmea
ms.openlocfilehash: b6b73fef368dc8dec5d8c042cdac1d3cea899450
ms.sourcegitcommit: dabd9eb9925308d3c2404c3957e5c921408089da
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 07/11/2020
ms.locfileid: "86242445"
---
# <a name="add-a-symbol-layer-to-a-map-using-azure-maps-android-sdk"></a>Adicione uma camada de símbolo a um mapa usando Azure Maps Android SDK

Este artigo mostra-lhe como renderizar dados de pontos de uma fonte de dados como uma camada de símbolo num mapa usando o Azure Maps Android SDK.

## <a name="prerequisites"></a>Pré-requisitos

Para seguir completamente os passos neste artigo, é necessário instalar [o Azure Maps Android SDK](https://docs.microsoft.com/azure/azure-maps/how-to-use-android-map-control-library) para carregar um mapa.

## <a name="add-a-symbol-layer"></a>Adicionar uma camada de símbolo

Para adicionar um marcador no mapa utilizando a camada de símbolo, siga os passos abaixo:

1. Editar **res**  >  **o layout**  >  ** resactivity_main.xml** para que pareça o seguinte XML:
    
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

2. Copie o seguinte corte de código no método **onCreate()** da sua `MainActivity.java` classe.

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
    
    O corte de código acima obtém primeiro uma instância de controlo do mapa Azure Maps utilizando o método de retorno **onReady().** Em seguida, cria um objeto de origem de dados utilizando a classe **DataSource** e adiciona-o ao mapa. Em seguida, adiciona-lhe uma **característica** que contém uma geometria de ponto. Uma imagem de marcador vermelho é então definida como ícone para o símbolo. Uma **camada de símbolo** utiliza texto ou ícones para tornar os dados baseados em ponto embrulhados na fonte de dados como símbolo no mapa. Uma camada de símbolo é então criada e a fonte de dados é passada para renderizar, e é adicionada às camadas do mapa.
    
    Depois de adicionar o corte de código acima, `MainActivity.java` deve parecer-se com o abaixo:
    
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
    
Neste ponto, se executar a sua aplicação, deve ver um marcador no mapa, como mostrado aqui:

<center>

![Pino do mapa android](./media/how-to-add-symbol-to-android-map/android-map-pin.png)</center>

> [!TIP]
> Por padrão, as camadas de símbolos otimizam a renderização de símbolos escondendo símbolos que se sobrepõem. À medida que se aproxima, os símbolos ocultos tornam-se visíveis. Para desativar esta função e tornar todos os símbolos em todos os momentos, desative `iconAllowOverlap` a opção para `true` .

## <a name="next-steps"></a>Passos seguintes

Para adicionar mais coisas ao seu mapa, consulte:

> [!div class="nextstepaction"]
> [Adicione formas a um mapa Android](https://docs.microsoft.com/azure/azure-maps/how-to-add-shapes-to-android-map)

> [!div class="nextstepaction"]
> [Apresentar informações da funcionalidade](display-feature-information-android.md)