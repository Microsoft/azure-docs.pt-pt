---
title: Adicione uma camada de azulejos aos mapas Android Microsoft Azure Maps
description: Aprenda a adicionar uma camada de azulejos a um mapa. Veja um exemplo que usa o Azure Maps Android SDK para adicionar uma sobreposição de radares meteorológicos a um mapa.
author: anastasia-ms
ms.author: v-stharr
ms.date: 04/26/2019
ms.topic: how-to
ms.service: azure-maps
services: azure-maps
manager: philmea
ms.openlocfilehash: faa5e3e3177e18787a73c6b4c43aa04b6bc760b7
ms.sourcegitcommit: 4064234b1b4be79c411ef677569f29ae73e78731
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/28/2020
ms.locfileid: "92897097"
---
# <a name="add-a-tile-layer-to-a-map-using-the-azure-maps-android-sdk"></a>Adicione uma camada de azulejo a um mapa usando o Azure Maps Android SDK

Este artigo mostra-lhe como renderizar uma camada de azulejos num mapa usando o Azure Maps Android SDK. As camadas de azulejos permitem-lhe sobrepor imagens em cima dos azulejos do mapa base do Azure Maps. Mais informações sobre o sistema de inclinação Azure Maps podem ser encontradas nos níveis de Zoom e documentação [da grelha de azulejos.](zoom-levels-and-tile-grid.md)

Uma camada de azulejos carrega em azulejos de um servidor. Estas imagens podem ser pré-renderizadas e armazenadas como qualquer outra imagem num servidor, usando uma convenção de nomeação que a camada de azulejos compreende. Ou, estas imagens podem ser renderizadas com um serviço dinâmico que gera as imagens perto do tempo real. Existem três diferentes convenções de nomeação de azulejos apoiadas pela classe Azure Maps TileLayer:

* X, Y, Notação zoom - Com base no nível de zoom, x é a coluna e y é a posição de linha do azulejo na grelha de azulejos.
* Notação quadkey - Combinação x, y, zoom informação em um único valor de corda que é um identificador único para um azulejo.
* Caixa limite - As coordenadas de caixa de limites podem ser usadas para especificar uma imagem no formato `{west},{south},{east},{north}` que é comumente utilizada pelos [Serviços de Mapeamento Web (WMS)](https://www.opengeospatial.org/standards/wms).

> [!TIP]
> Um TileLayer é uma ótima maneira de visualizar grandes conjuntos de dados no mapa. Não só uma camada de azulejo pode ser gerada a partir de uma imagem, mas os dados vetoriais também podem ser renderizados como uma camada de azulejos. Ao renderizar dados vetoriais como uma camada de azulejos, o controlo do mapa só precisa de carregar os azulejos que podem ser muito menores no tamanho do ficheiro do que os dados vetoriais que representam. Esta técnica é usada por muitos que precisam de renderizar milhões de linhas de dados no mapa.

O URL de azulejos passados para uma camada de azulejos deve ser um URL http/https para um recurso TileJSON ou um modelo de URL de azulejos que utiliza os seguintes parâmetros: 

* `{x}` - X posição do azulejo. Também precisa `{y}` `{z}` e. .
* `{y}` - Posição Y do azulejo. Também precisa `{x}` `{z}` e. .
* `{z}` - Zoom nível do azulejo. Também precisa `{x}` `{y}` e. .
* `{quadkey}` - Identificador de azulejos de azulejos baseado na convenção de nomeação do sistema de azulejos Bing Maps.
* `{bbox-epsg-3857}` - Uma cadeia de caixa de limitação com o formato `{west},{south},{east},{north}` no Sistema de Referência Espacial EPSG 3857.
* `{subdomain}` - Um espaço reservado para os valores do subdomínio, se o valor do subdomínio for especificado.

## <a name="prerequisites"></a>Pré-requisitos

Para completar o processo neste artigo, precisa instalar [o Azure Maps Android SDK](./how-to-use-android-map-control-library.md) para carregar um mapa.


## <a name="add-a-tile-layer-to-the-map"></a>Adicione uma camada de azulejo ao mapa

 Esta amostra mostra como criar uma camada de azulejos que aponta para um conjunto de azulejos. Estes azulejos usam o sistema de inclinação "x, y, zoom". A fonte desta camada de azulejos é uma sobreposição de radares meteorológicos do [Mesonet Ambiental de Iowa da Universidade Estadual de Iowa.](https://mesonet.agron.iastate.edu/ogc/) 

Pode adicionar uma camada de azulejos ao mapa seguindo os passos abaixo.

1. Editar **res > layout > activity_main.xml** para que pareça o de baixo:

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

2. Copie o seguinte corte de código abaixo no método **onCreate()** da sua `MainActivity.java` classe.

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
    
    O corte de código acima obtém primeiro uma instância de controlo do mapa Azure Maps utilizando o método de retorno **onReady().** Em seguida, cria um `TileLayer` objeto e passa um URL de **azulejos xyz** formatados na `tileUrl` opção. A opacidade da camada é definida para `0.8` e uma vez que os azulejos do serviço de azulejos que estão sendo usados são 256 pixels, esta informação é passada para a `tileSize` opção. A camada de azulejo é então passada para o gestor da camada de mapas.

    Depois de adicionar o corte de código acima, `MainActivity.java` deve parecer-se com o abaixo:
    
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
> [Alterar estilos de mapa em mapas Android](./set-android-map-styles.md)