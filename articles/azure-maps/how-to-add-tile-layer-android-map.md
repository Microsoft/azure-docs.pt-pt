---
title: Adicione uma camada de azulejos aos mapas Android / Microsoft Azure Maps
description: Neste artigo, você vai aprender a render uma camada de azulejos num mapa usando o Microsoft Azure Maps Android SDK.
author: philmea
ms.author: philmea
ms.date: 04/26/2019
ms.topic: conceptual
ms.service: azure-maps
services: azure-maps
manager: philmea
ms.openlocfilehash: f98598bd1307bb1b46ff23814780c5f809b9ac90
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/28/2020
ms.locfileid: "80335570"
---
# <a name="add-a-tile-layer-to-a-map-using-the-azure-maps-android-sdk"></a>Adicione uma camada de azulejos a um mapa usando o Azure Maps Android SDK

Este artigo mostra-lhe como renderizar uma camada de azulejos num mapa usando o Azure Maps Android SDK. As camadas de azulejos permitem-lhe sobrepor imagens em cima dos azulejos do mapa base do Azure Maps. Mais informações sobre o sistema de azulejos Do Azure Maps podem ser encontradas nos níveis de Zoom e na documentação da [grelha de azulejos.](zoom-levels-and-tile-grid.md)

Uma camada de azulejos carrega em azulejos de um servidor. Estas imagens podem ser pré-renderizadas e armazenadas como qualquer outra imagem num servidor, utilizando uma convenção de nomeação que a camada de azulejos entende. Ou, estas imagens podem ser renderizadas com um serviço dinâmico que gera as imagens em tempo real. Existem três diferentes convenções de nomeação de serviços de azulejos apoiadas pela classe Azure Maps TileLayer:

* X, Y, Zoom notação - Com base no nível de zoom, x é a coluna e y é a posição da linha do azulejo na grelha de azulejos.
* Notação quadkey - Combinação x, y, zoom informação em um único valor de corda que é um identificador único para um azulejo.
* Caixa de delimitação - As coordenadas de caixa `{west},{south},{east},{north}` de delimitação podem ser utilizadas para especificar uma imagem no formato que é comumente utilizado pelos Serviços de [Mapeamento Web (WMS)](https://www.opengeospatial.org/standards/wms).

> [!TIP]
> A TileLayer é uma ótima maneira de visualizar grandes conjuntos de dados no mapa. Não só uma camada de azulejo pode ser gerada a partir de uma imagem, como os dados vetoriais também podem ser renderizados como uma camada de azulejos também. Ao tornar os dados vetoriais como uma camada de azulejos, o controlo do mapa só precisa de carregar os azulejos que podem ser muito menores no tamanho do ficheiro do que os dados vetoriais que representam. Esta técnica é usada por muitos que precisam de renderizar milhões de linhas de dados no mapa.

O URL de azulejos passado para uma camada de azulejos deve ser um URL http/https para um recurso TileJSON ou um modelo de URL de azulejos que utilize os seguintes parâmetros: 

* `{x}`- Posição X do azulejo. Também `{y}` precisa `{z}`e.
* `{y}`- Posição Y do azulejo. Também `{x}` precisa `{z}`e.
* `{z}`- Nível de zoom do azulejo. Também `{x}` precisa `{y}`e.
* `{quadkey}`- Identificador quadkey de azulejos baseado na convenção de nomeação do sistema de azulejos Bing Maps.
* `{bbox-epsg-3857}`- Uma cadeia de caixa `{west},{south},{east},{north}` de delimitação com o formato no Sistema de Referência Espacial EPSG 3857.
* `{subdomain}`- Espaço reservado para os valores do subdomínio, se for especificado o valor do subdomínio.

## <a name="prerequisites"></a>Pré-requisitos

Para completar o processo neste artigo, é necessário instalar o [Azure Maps Android SDK](https://docs.microsoft.com/azure/azure-maps/how-to-use-android-map-control-library) para carregar um mapa.


## <a name="add-a-tile-layer-to-the-map"></a>Adicione uma camada de azulejos ao mapa

 Esta amostra mostra como criar uma camada de azulejos que aponta para um conjunto de azulejos. Estes azulejos usam o sistema de tiling "x, y, zoom". A fonte desta camada de azulejos é uma sobreposição de radares meteorológicos do [Iowa Environmental Mesonet da Universidade Estadual de Iowa.](https://mesonet.agron.iastate.edu/ogc/) 

Pode adicionar uma camada de azulejo ao mapa seguindo os passos abaixo.

1. Editar **res > layout > activity_main.xml** para que se pareça com o abaixo:

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
            app:mapcontrol_centerLat="40.75"
            app:mapcontrol_centerLng="-99.47"
            app:mapcontrol_zoom="3"
            />
    
    </FrameLayout>
    ```

2. Copie o seguinte código abaixo no método **onCreate()** da sua `MainActivity.java` classe.

    ```Java
    mapControl.onReady(map -> {
        //Add a tile layer to the map, below the map labels.
        map.layers.add(new TileLayer(
            tileUrl("https://mesonet.agron.iastate.edu/cache/tile.py/1.0.0/nexrad-n0q-900913/{z}/{x}/{y}.png"),
            opacity(0.8f),
            tileSize(256)
        ), "labels");
    });
    ```
    
    O código acima obtém primeiro uma instância de controlo de mapas Do Mapa Azure Maps utilizando o método de chamada **onReady().** Em seguida, `TileLayer` cria um objeto e passa um URL `tileUrl` de **azulejos de xiz** formatado para a opção. A opacidade da camada `0.8` está definida e uma vez que os azulejos do serviço de `tileSize` azulejos que estão a ser utilizados são de 256 pixels, esta informação é transmitida para a opção. A camada de azulejos é então passada para o gestor de camadas de mapas.

    Depois de adicionar o código `MainActivity.java` acima, o seu deve parecer o abaixo:
    
    ```Java
    package com.example.myapplication;

    import android.app.Activity;
    import android.os.Bundle;
    import android.support.v7.app.AppCompatActivity;
    import com.microsoft.azure.maps.mapcontrol.layer.TileLayer;
    import java.util.Arrays;
    import java.util.List;
    import com.microsoft.azure.maps.mapcontrol.AzureMaps;
    import com.microsoft.azure.maps.mapcontrol.MapControl;
    import static com.microsoft.azure.maps.mapcontrol.options.TileLayerOptions.tileSize;
    import static com.microsoft.azure.maps.mapcontrol.options.TileLayerOptions.tileUrl;
        
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

                //Add a tile layer to the map, below the map labels.
                map.layers.add(new TileLayer(
                    tileUrl("https://mesonet.agron.iastate.edu/cache/tile.py/1.0.0/nexrad-n0q-900913/{z}/{x}/{y}.png"),
                    opacity(0.8f),
                    tileSize(256)
                ), "labels");
            });    
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

Se executar a sua aplicação agora, deve ver uma linha no mapa como visto abaixo:

<center>

![Linha de mapa android](./media/how-to-add-tile-layer-android-map/xyz-tile-layer-android.png)</center>

## <a name="next-steps"></a>Passos seguintes

Veja o seguinte artigo para saber mais sobre formas de definir estilos de mapa

> [!div class="nextstepaction"]
> [Alterar estilos de mapa em mapas Android](https://docs.microsoft.com/azure/azure-maps/set-android-map-styles)