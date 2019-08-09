---
title: Adicionar uma camada de bloco aos mapas do Android no Azure Maps | Microsoft Docs
description: Como adicionar uma camada de bloco a um mapa usando o Azure Maps SDK do Android
author: walsehgal
ms.author: v-musehg
ms.date: 04/26/2019
ms.topic: conceptual
ms.service: azure-maps
services: azure-maps
manager: philmea
ms.openlocfilehash: 5d5f50a38db95f6e62bdd8c51aefd5957041e682
ms.sourcegitcommit: aa042d4341054f437f3190da7c8a718729eb675e
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 08/09/2019
ms.locfileid: "68886614"
---
# <a name="add-a-tile-layer-to-a-map-using-the-azure-maps-android-sdk"></a>Adicionar uma camada de bloco a um mapa usando o Azure Maps SDK do Android

Este artigo mostra como renderizar uma camada de bloco em um mapa usando o SDK do Android do Azure Maps. As camadas de bloco permitem sobreimpor imagens na parte superior dos blocos de mapa base do Azure Maps. Mais informações sobre o sistema de divisão de mapas do Azure podem ser encontradas na documentação [níveis de zoom e grade de blocos](zoom-levels-and-tile-grid.md) .

Uma camada de bloco é carregada em blocos de um servidor. Essas imagens podem ser previamente renderizadas e armazenadas como qualquer outra imagem em um servidor usando uma Convenção de nomenclatura que a camada de bloco entenda ou um serviço dinâmico que gera as imagens em tempo real. Há três convenções de nomenclatura de serviço de bloco diferentes com suporte pela classe TileLayer do Azure Maps; 

* X, Y, aplicar notação de zoom-com base no nível de zoom, x é a coluna e Y é a posição de linha do bloco na grade de blocos.
* Notação de Quadkey – combinação x, y, informações de zoom em um único valor de cadeia de caracteres que é um identificador exclusivo para um bloco.
* A caixa delimitadora – as coordenadas da caixa delimitadora podem ser usadas para especificar `{west},{south},{east},{north}` uma imagem no formato que é normalmente usado pelos [serviços de mapeamento da Web (WMS)](https://www.opengeospatial.org/standards/wms).

> [!TIP]
> Um TileLayer é uma ótima maneira de Visualizar grandes conjuntos de dados no mapa. Uma camada de bloco não só pode ser gerada a partir de uma imagem, mas os dados vetoriais também podem ser renderizados como uma camada de bloco. Ao renderizar dados de vetor como uma camada de bloco, o controle de mapa só precisa carregar os blocos que podem ser muito menores no tamanho do arquivo do que os dados de vetor que eles representam. Essa técnica é usada por muitos que precisam renderizar milhões de linhas de dados no mapa.

A URL do bloco passada para uma camada de bloco deve ser uma URL http/https para um recurso TileJSON ou um modelo de URL de bloco que usa os seguintes parâmetros: 

* `{x}`-X posição do bloco. Também precisa `{y}` de `{z}`e.
* `{y}`-A posição Y do bloco. Também precisa `{x}` de `{z}`e.
* `{z}`-Nível de zoom do bloco. Também precisa `{x}` de `{y}`e.
* `{quadkey}`-O identificador de quadkey de bloco com base na Convenção de nomenclatura do sistema de blocos do Bing Maps.
* `{bbox-epsg-3857}`-Uma cadeia de caracteres de caixa delimitadora com o formato `{west},{south},{east},{north}` no sistema de referência espacial do EPSG 3857.
* `{subdomain}`-Um espaço reservado onde os valores de subdomínio se especificados serão adicionados.

## <a name="prerequisites"></a>Pré-requisitos

Para concluir o processo neste artigo, você precisa instalar o [Azure Maps SDK do Android](https://docs.microsoft.com/azure/azure-maps/how-to-use-android-map-control-library) para carregar um mapa.


## <a name="add-a-tile-layer-to-the-map"></a>Adicionar uma camada de bloco ao mapa

 Este exemplo mostra como criar uma camada de peça que aponta para um conjunto de blocos que usam o sistema de divisão x, y e zoom. A origem dessa camada de peça é uma sobreposição de radar do [Iowa ambiente Mesonet da Universidade de estado do Iowa](https://mesonet.agron.iastate.edu/ogc/). 

Você pode adicionar uma camada de bloco ao mapa seguindo as etapas abaixo.

1. Edite o **layout de > res > activity_main. xml** para que ele se pareça com o seguinte:

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

2. Copie o trecho de código a seguir para o método **OnCreate ()** da `MainActivity.java` sua classe.

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
    
    O trecho de código acima Obtém primeiro uma instância de controle de mapa do Azure Maps usando o método de retorno de chamada onreadal **()** . Em seguida, ele `TileLayer` cria um objeto e passa uma URL de bloco do `tileUrl` **XYZ** formatado para a opção. A opacidade da camada é definida como `0.8` e como os blocos do serviço de bloco que estão sendo usados são blocos de 256 pixels, essas informações são passadas para a `tileSize` opção. Em seguida, a camada de bloco é passada para o Gerenciador de camadas do Maps.

    Depois de adicionar o trecho de código acima `MainActivity.java` , seu deve se parecer com o seguinte:
    
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

Se você executar o aplicativo agora, deverá ver uma linha no mapa, como mostrado abaixo:

<center>

![Linha do mapa do Android](./media/how-to-add-tile-layer-android-map/xyz-tile-layer-android.png)</center>

## <a name="next-steps"></a>Passos Seguintes

Consulte o artigo a seguir para saber mais sobre maneiras de definir estilos de mapa

> [!div class="nextstepaction"]
> [Alterar estilos de mapa em mapas do Android](https://docs.microsoft.com/azure/azure-maps/set-android-map-styles)