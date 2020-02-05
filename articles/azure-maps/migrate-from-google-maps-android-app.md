---
title: 'Tutorial: migrar um aplicativo Android | Mapas do Microsoft Azure'
description: Como migrar um aplicativo Android do Google Maps para mapas de Microsoft Azure.
author: rbrundritt
ms.author: richbrun
ms.date: 12/17/2019
ms.topic: tutorial
ms.service: azure-maps
services: azure-maps
manager: cpendle
ms.custom: ''
ms.openlocfilehash: 6e54d8ea44b6c322f311cc1baeb6ca3ab6715aee
ms.sourcegitcommit: 4f6a7a2572723b0405a21fea0894d34f9d5b8e12
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 02/04/2020
ms.locfileid: "76989965"
---
# <a name="migrate-an-android-app-from-google-maps"></a>Migrar um aplicativo Android do Google Maps

O Azure Maps Android SDK tem uma interface API semelhante ao Web SDK. Se desenvolveu com um destes SDKs, muitos dos mesmos conceitos, boas práticas e arquiteturas se aplicam. Deve ser capaz de transferir facilmente o seu conhecimento de um para o outro.

O Azure Maps SDK do Android dá suporte a uma versão mínima do Android da API 21: Android 5.0.0 (pirulito).

Todos os exemplos são fornecidos em Java, no entanto Kotlin também pode ser usado com o Azure Maps Android SDK.

Para obter mais informações sobre o desenvolvimento com o Android SDK by Azure Maps, consulte os [guias how-to para o Azure Maps Android SDK](how-to-use-android-map-control-library.md).

## <a name="load-a-map"></a>Carregar um mapa

Carregar um mapa em um aplicativo Android usando o Google ou o Azure Maps consiste em muitas das mesmas etapas. Ao utilizar qualquer um dos SDK, deve:

- Obtenha uma API ou chave de assinatura para acessar qualquer uma das plataformas.
- Adicione um XML a uma atividade para especificar onde o mapa deve ser renderizado e como ele deve ser disposto.
- Encaminhe todos os métodos de ciclo de vida da atividade que contém a exibição de mapa para os correspondentes na classe de mapa. Em particular, deve anular os seguintes métodos:
    - `onCreate(Bundle)`
    - `onStart()`
    - `onResume()`
    - `onPause()`
    - `onStop()`
    - `onDestroy()`
    - `onSaveInstanceState(Bundle)`
    - `onLowMemory()`
- Aguarde até que o mapa esteja pronto antes de tentar acessá-lo programaticamente.

**Antes: Google Maps**

Para exibir um mapa usando o SDK do Google Maps para Android, as etapas a seguir serão executadas:

1.  Verifique se os serviços do Google Play estão instalados.
2.  Adicione uma dependência para o serviço do Google Maps ao arquivo **gradle. Build** do módulo: 

    `implementation 'com.google.android.gms:play-services-maps:17.0.0'`

1.  Adicione uma chave API do Google Maps dentro da secção de aplicação do google\_mapas\_ficheiro **api.xml:**
    
    ```xml
    <meta-data android:name="com.google.android.geo.API_KEY" android:value="YOUR_GOOGLE_MAPS_KEY"/>
    ```

1.  Adicione um fragmento de mapa à atividade principal:

    ```xml
    <com.google.android.gms.maps.MapView
            android:id="@+id/myMap"
            android:layout_width="match_parent"
            android:layout_height="match_parent"/>
    ```

1.  No ficheiro MainActivity.java, terá de adicionar importações para o Google Maps SDK. Encaminhe todos os métodos de ciclo de vida da atividade que contém a exibição de mapa para os correspondentes na classe de mapa. Uma instância de `MapView` pode ser recuperada do fragmento do mapa usando o método `getMapAsync(OnMapReadyCallback)`. O `MapView` inicializa automaticamente o sistema de mapas e a exibição. Edite o arquivo **MainActivity. java** da seguinte maneira:

    ```java
    import com.google.android.gms.maps.GoogleMap;
    import com.google.android.gms.maps.MapView;
    import com.google.android.gms.maps.OnMapReadyCallback;
    
    import android.support.v7.app.AppCompatActivity;
    import android.os.Bundle;
    
    public class MainActivity extends AppCompatActivity implements OnMapReadyCallback {
    
        MapView mapView;
    
        @Override
        protected void onCreate(Bundle savedInstanceState) {
            super.onCreate(savedInstanceState);
            setContentView(R.layout.activity_main);
    
            mapView = findViewById(R.id.myMap);
    
            mapView.onCreate(savedInstanceState);
            mapView.getMapAsync(this);
        }
    
        @Override
    
        public void onMapReady(GoogleMap map) {
            //Add your post map load code here.
        }
    
        @Override
        public void onResume() {
            super.onResume();
            mapView.onResume();
        }
    
        @Override
        protected void onStart(){
            super.onStart();
            mapView.onStart();
        }
    
        @Override
        public void onPause() {
            super.onPause();
            mapView.onPause();
        }
    
        @Override
        public void onStop() {
            super.onStop();
            mapView.onStop();
        }
    
        @Override
        public void onLowMemory() {
            super.onLowMemory();
            mapView.onLowMemory();
        }
    
        @Override
        protected void onDestroy() {
            super.onDestroy();
            mapView.onDestroy();
        }
    
        @Override
        protected void onSaveInstanceState(Bundle outState) {
            super.onSaveInstanceState(outState);
            mapView.onSaveInstanceState(outState);
        }
    }
    ```

Quando executou uma aplicação, o controlo do mapa carregou da seguinte forma.

<center>

![](media/migrate-google-maps-android-app/simple-google-maps.png)</center> simples do Google Maps

**Após: mapas do Azure**

Para exibir um mapa usando o SDK do Azure Maps para Android, as etapas a seguir precisam ser feitas:

1. Abra o ficheiro **build.gradle** de nível superior e adicione o seguinte código à secção de todos **os projetos:**

    ```JAVA
    maven {
            url "https://atlas.microsoft.com/sdk/android"
    }
    ```

2. Atualize seu **app/Build. gradle** e adicione o seguinte código a ele:
    
    1. Verifique se o **minSdkVersion** do seu projeto está na API 21 ou superior.

    2. Adicione o seguinte código à seção do Android:

        ```JAVA
        compileOptions {
            sourceCompatibility JavaVersion.VERSION_1_8
            targetCompatibility JavaVersion.VERSION_1_8
        }
        ```
    3. Atualize seu bloco de dependências e adicione uma nova linha de dependência de implementação para os mais recentes SDK do Android do Azure Maps:

        ```JAVA
        implementation "com.microsoft.azure.maps:mapcontrol:0.2"
        ```

        > [!Note]
        > O Azure Maps SDK do Android está sendo atualizado e aprimorado regularmente. Pode ver o Get iniciado com documentação de controlo de [mapas Android,](how-to-use-android-map-control-library.md) para obter o mais recente número da versão Do Azure Maps. Além disso, pode definir o número da versão de "0.2" para "0+" para que o seu código aponte sempre para a versão mais recente.
    
    4. Vá para **arquivo** na barra de ferramentas e clique em **sincronizar projeto com arquivos gradle**.
3. Adicione um fragmento de mapa à atividade principal (recursos \> layout \> atividade\_main.xml):
    
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
            />
    </FrameLayout>
    ```

4. No ficheiro **MainActivity.java** terá de:
    
    * Adicionar importações para o SDK do Azure Maps
    * definir as informações de autenticação do Azure Maps
    * obter a instância de controle de mapa no método **OnCreate**

    Definir as informações de autenticação na classe `AzureMaps` globalmente usando os métodos `setSubscriptionKey` ou `setAadProperties` faz isso para que você não precise adicionar suas informações de autenticação a cada exibição. 

    O controle de mapa contém seus próprios métodos de ciclo de vida para gerenciar o ciclo de vida do OpenGL do Android, que deve ser chamado diretamente da atividade que o contém. Para chamar corretamente os métodos de ciclo de vida do controlo do mapa, deve substituir os seguintes métodos de ciclo de vida na Atividade que contém o controlo do mapa e chamar o respetivo método de controlo do mapa. 

    * `onCreate(Bundle)` 
    * `onStart()` 
    * `onResume()` 
    * `onPause()` 
    * `onStop()` 
    * `onDestroy()` 
    * `onSaveInstanceState(Bundle)` 
    * `onLowMemory()`

    Edite o arquivo **MainActivity. java** da seguinte maneira:
    
    ```java
    package com.example.myapplication;

    import android.support.v7.app.AppCompatActivity;
    import android.os.Bundle;
    import com.microsoft.azure.maps.mapcontrol.AzureMaps;
    import com.microsoft.azure.maps.mapcontrol.MapControl;
    import com.microsoft.azure.maps.mapcontrol.layer.SymbolLayer;
    import com.microsoft.azure.maps.mapcontrol.options.MapStyle;
    import com.microsoft.azure.maps.mapcontrol.source.DataSource;

    public class MainActivity extends AppCompatActivity {
        
        static {
            AzureMaps.setSubscriptionKey("<Your Azure Maps subscription key>");
        }

        MapControl mapControl;

        @Override
        protected void onCreate(Bundle savedInstanceState) {
            super.onCreate(savedInstanceState);
            setContentView(R.layout.activity_main);

            mapControl = findViewById(R.id.mapcontrol);

            mapControl.onCreate(savedInstanceState);
    
            //Wait until the map resources are ready.
            mapControl.onReady(map -> {
                //Add your post map load code here.
    
            });
        }

        @Override
        public void onResume() {
            super.onResume();
            mapControl.onResume();
        }

        @Override
        protected void onStart(){
            super.onStart();
            mapControl.onStart();
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

Se você executar o aplicativo, o controle de mapa será carregado da seguinte maneira.

<center>

![](media/migrate-google-maps-android-app/simple-azure-maps.png)</center> simples de mapas azure

Observe que o controle de mapas do Azure dá suporte à ampliação de mais e fornece uma exibição mundial.

> [!TIP]
> Se estiver usando um emulador do Android no Windows, o mapa poderá não ser renderizado devido a conflitos com o OpenGL e a renderização de gráficos acelerada por software. O seguinte procedimento funcionou para resolver esse problema. Abra o Gerenciador de AVD e selecione o dispositivo virtual a ser editado. Desloque-se para baixo do painel **de configuração de verificação.** Na seção **desempenho emulado** , defina a opção **gráficos** como **hardware**.

## <a name="localizing-the-map"></a>Localizando o mapa

Se seu público estiver espalhado em vários países ou falar em idiomas diferentes, a localização será importante.

**Antes: Google Maps**

A linguagem do mapa pode ser definida no método `onCreate` da atividade principal, adicionando o seguinte código. O código deve ser adicionado antes de definir a visão de contexto do mapa. O seguinte limita o idioma ao francês usando o código de idioma "fr".

```java
String languageToLoad = "fr";
Locale locale = new Locale(languageToLoad);
Locale.setDefault(locale);
Configuration config = new Configuration();
config.locale = locale;
getBaseContext().getResources().updateConfiguration(config,
        getBaseContext().getResources().getDisplayMetrics());
```

Aqui está um exemplo do Google Maps com o idioma definido como "fr".

<center>

![](media/migrate-google-maps-android-app/google-maps-localization.png)</center> de localização do Google Maps

**Após: mapas do Azure**

O mapas do Azure fornece três maneiras diferentes de definir o idioma e a exibição regional do mapa. A primeira opção é passar as informações de idioma e de exibição regional para a classe `AzureMaps` usando os métodos `setLanguage` e `setView` estáticos globalmente. Isso definirá o idioma padrão e a exibição regional em todos os controles do Azure Maps carregados em seu aplicativo. O seguinte limita o idioma ao francês usando o código de idioma "fr-FR".

```java
static {
    //Set your Azure Maps Key.
    AzureMaps.setSubscriptionKey("<Your Azure Maps Key>");

    //Set the language to be used by Azure Maps.
    AzureMaps.setLanguage("fr-FR");

    //Set the regional view to be used by Azure Maps.
    AzureMaps.setView("auto");
}
```

A segunda opção é passar o idioma e exibir informações para o XML de controle de mapa.

```xml
<com.microsoft.azure.maps.mapcontrol.MapControl
    android:id="@+id/myMap"
    android:layout_width="match_parent"
    android:layout_height="match_parent"
    app:mapcontrol_language="fr-FR"
    app:mapcontrol_view="auto"
    />
```

A terceira opção é definir programaticamente o idioma e a exibição regional do mapa usando o método Maps `setStyle`. Esta opção pode ser definida a qualquer momento para alterar a linguagem e a visão regional do mapa.

```java
mapControl.onReady(map -> {
    map.setStyle(StyleOptions.language("fr-FR"));
    map.setStyle(StyleOptions.view("auto"));
});
```

Aqui está um exemplo de mapas do Azure com o idioma definido como "fr-FR".

<center>

![Azure Maps](media/migrate-google-maps-android-app/azure-maps-localization.png)</center> de localização

Uma lista completa de idiomas com suporte e exibições regionais está documentada [aqui](supported-languages.md).

## <a name="setting-the-map-view"></a>Configurando a exibição do mapa

Mapas dinâmicos tanto no Azure Maps como no Google Maps podem ser transferidos programáticamente para novos locais geográficos, chamando os métodos apropriados. Os exemplos abaixo mostram como fazer o mapa exibir imagens aéreas de satélite, centrar o mapa sobre um local com coordenadas (latitude: 35.0272, longitude: -111.0225) e alterar o nível de zoom para 15 no Google Maps.

> [!NOTE]
> O Google Maps usa blocos que são 256 pixels em dimensões enquanto o Azure Maps usa um bloco maior de 512 pixels. Isso reduz o número de solicitações de rede necessárias ao mapas do Azure para carregar a mesma área do mapa que o Google Maps. No entanto, devido à maneira como as pirâmides de bloco funcionam nos controles de mapa, os blocos maiores no Azure Maps significam que para alcançar essa mesma área visível como um mapa no Google Maps, você precisa subtrair o nível de zoom usado no Google Maps em 1 ao usar mapas do Azure. 

**Antes: Google Maps**

A câmara do controlo de mapas do Google Maps pode ser deslocada programáticamente utilizando o método `moveCamera`, que permite especificar o centro do mapa e um nível de zoom. O método `setMapType` pode ser usado para alterar o tipo de mapa exibido.

```java
@Override
public void onMapReady(GoogleMap googleMap) {
    mapView = googleMap;

    mapView.moveCamera(CameraUpdateFactory.newLatLngZoom(new LatLng(35.0272, -111.0225), 15));
    mapView.setMapType(GoogleMap.MAP_TYPE_SATELLITE);
}
```

<center>

![google Maps definir vista](media/migrate-google-maps-android-app/google-maps-set-view.png)</center>

**Após: mapas do Azure**

Conforme observado anteriormente, para obter a mesma área visível nos mapas do Azure, subtraia o nível de zoom usado no Google Maps por um, neste caso, use um nível de zoom de 14.

O modo de exibição de mapa inicial pode ser definido em atributos XML no controle de mapa.

```xml
<com.microsoft.azure.maps.mapcontrol.MapControl
    android:id="@+id/myMap"
    android:layout_width="match_parent"
    android:layout_height="match_parent"
    app:mapcontrol_cameraLat="35.0272"
    app:mapcontrol_cameraLng="-111.0225"
    app:mapcontrol_zoom="14"
    app:mapcontrol_style="satellite"
    />
```

A exibição de mapa pode ser atualizada programaticamente usando os métodos `setCamera` e `setStyle` do Maps.

```java
mapControl.onReady(map -> {
    //Set the camera of the map.
    map.setCamera(center(35.0272, -111.0225), zoom(14));

    //Set the style of the map.
    map.setStyle(style(MapStyle.SATELLITE));
});
```

<center>

![Azure Maps definir vista](media/migrate-google-maps-android-app/azure-maps-set-view.png)</center>

**Recursos adicionais:**

- [Estilos de mapa com suporte](supported-map-styles.md)

## <a name="adding-a-marker"></a>Adicionando um marcador

Os dados de ponto geralmente são renderizados no mapa usando uma imagem no mapa. Essas imagens são geralmente chamadas de marcadores, anotações, Pins ou símbolos. Os exemplos a seguir renderizam dados de ponto como marcadores no mapa em (Latitude: 51,5, Longitude:-0,2).

**Antes: Google Maps**

Com o Google Maps, os marcadores são adicionados usando o método Maps `addMarker`.

```java
@Override
public void onMapReady(GoogleMap googleMap) {
    mapView = googleMap;

    mapView.addMarker(new MarkerOptions().position(new LatLng(47.64, -122.33)));
}
```

<center>

![](media/migrate-google-maps-android-app/google-maps-marker.png)</center> de marcadores do Google Maps

**Após: mapas do Azure**

No Azure Maps, os dados de pontos podem ser renderizados no mapa adicionando primeiro os dados a uma fonte de dados. Em seguida, ligando essa fonte de dados a uma camada de símbolo. A fonte de dados otimiza a gestão de dados espaciais no controlo do mapa. A camada de símbolo especifica como renderizar dados de pontos usando como imagem e/ou texto.

```java
mapControl.onReady(map -> {
    //Create a data source and add it to the map.
    DataSource dataSource = new DataSource();
    map.sources.add(dataSource);

    //Create a point feature and add it to the data source.
    dataSource.add(Feature.fromGeometry(Point.fromLngLat(-122.33, 47.64)));

    //Create a symbol layer and add it to the map.
    map.layers.add(new SymbolLayer(dataSource));
});
```

<center>

![azure Maps](media/migrate-google-maps-android-app/azure-maps-marker.png)</center>

## <a name="adding-a-custom-marker"></a>Adicionando um marcador personalizado

Imagens personalizadas podem ser usadas para representar pontos em um mapa. O mapa nos exemplos abaixo usa uma imagem personalizada para exibir um ponto no mapa. O ponto é de latitude: 51,5 e longitude: -0,2. O mapa compensa a posição do marcador de modo a que o ponto do ícone do pino se alinhe com a posição correta no mapa.

<center>

![imagem de pino amarelo](media/migrate-google-maps-web-app/ylw_pushpin.png)<br/>
ylw\_pushpin.png</center>

Em ambos os exemplos, a imagem acima é adicionada à pasta drawable dos recursos das apps.

**Antes: Google Maps**

Com o Google Maps, as imagens personalizadas podem ser usadas para marcadores. Carregue imagens personalizadas utilizando a opção `icon` do marcador. Para alinhar o ponto da imagem com a coordenada, utilize a opção `anchor`. A âncora é relativa às dimensões da imagem, neste caso, 0,2 unidades de largura e 1 unidade alta.

```java
@Override
public void onMapReady(GoogleMap googleMap) {
    mapView = googleMap;

    mapView.addMarker(new MarkerOptions().position(new LatLng(47.64, -122.33))
    .icon(BitmapDescriptorFactory.fromResource(R.drawable.ylw_pushpin))
    .anchor(0.2f, 1f));
}
```

<center>

![](media/migrate-google-maps-android-app/google-maps-custom-marker.png)</center> de marcador personalizado do Google Maps

**Após: mapas do Azure**

As camadas de símbolo no Azure Maps suportam imagens personalizadas, mas primeiro, a imagem precisa de ser carregada nos recursos do mapa e atribuída uma identificação única. Em seguida, a camada de símbolo pode fazer referência a essa ID. Offset o símbolo para alinhar com o ponto correto na imagem utilizando a opção `iconOffset`. O ícone compensado está em pixels. Por padrão, a compensação é relativa ao centro inferior da imagem, mas este valor de compensação pode ser ajustado utilizando a opção `iconAnchor`. Este exemplo define a opção `iconAnchor` como `"center"` e usa um deslocamento de ícone para mover a imagem em cinco pixels para a direita e 15 pixels para alinhar com o ponto da imagem do pino.

```java
mapControl.onReady(map -> {
    //Create a data source and add it to the map.
    DataSource dataSource = new DataSource();
    map.sources.add(dataSource);

    //Create a point feature and add it to the data source.
    dataSource.add(Feature.fromGeometry(Point.fromLngLat(-122.33, 47.64)));

    //Load the custom image icon into the map resources.
    map.images.add("my-yellow-pin", R.drawable.ylw_pushpin);

    //Create a symbol that uses the custom image icon and add it to the map.
    map.layers.add(new SymbolLayer(dataSource,
        iconImage("my-yellow-pin"),
        iconAnchor(AnchorType.CENTER
        iconOffset(new Float[]{5f, -15f})));
});
```

<center>

![Azure Maps](media/migrate-google-maps-android-app/azure-maps-custom-marker.png)</center> de marcador personalizado

## <a name="adding-a-polyline"></a>Adicionando uma polilinha

As polilinhas são usadas para representar uma linha ou um caminho no mapa. Os exemplos a seguir mostram como criar uma polilinha tracejada no mapa.

**Antes: Google Maps**

Com o Google Maps, uma polilinha pode ser criada usando a classe `PolylineOptions` e adicionada ao mapa usando o método `addPolyline`. A cor do traço pode ser definida usando a opção `color`, a largura do traço é definida usando a opção largura e uma matriz de traço tracejado pode ser definida usando a opção `pattern`.

```java
@Override
public void onMapReady(GoogleMap googleMap) {
    mapView = googleMap;

    //Create the options for the polyline.
    PolylineOptions lineOptions = new PolylineOptions()
            .add(new LatLng(46, -123))
            .add(new LatLng(49, -122))
            .add(new LatLng(46, -121))
            .color(Color.RED)
            .width(10f)
            .pattern(Arrays.<PatternItem>asList(
                    new Dash(30f), new Gap(30f)));

    //Add the polyline to the map.
    Polyline polyline = mapView.addPolyline(lineOptions);
}
```

<center>

![](media/migrate-google-maps-android-app/google-maps-polyline.png)</center> poliline do Google Maps

**Após: mapas do Azure**

Nos Mapas Azure, os polilines são chamados objetos `LineString` ou `MultiLineString`. Esses objetos podem ser adicionados a uma fonte de dados e renderizados usando uma camada de linha. As unidades de largura de traçado e de traço "pixel" alinham-se com o Azure Maps Web SDK, na forma como a utilização dos mesmos valores em ambos os SDKs produz os mesmos resultados.

```java
mapControl.onReady(map -> {
    //Create a data source and add it to the map.
    DataSource dataSource = new DataSource();
    map.sources.add(dataSource);

    //Create an array of points.
    List<Point> points = Arrays.asList(
        Point.fromLngLat(-123, 46),
        Point.fromLngLat(-122, 49),
        Point.fromLngLat(-121, 46));

    //Create a LineString feature and add it to the data source.
    dataSource.add(Feature.fromGeometry(LineString.fromLngLats(points)));

    //Create a line layer and add it to the map.
    map.layers.add(new LineLayer(dataSource,
        strokeColor("red"),
        strokeWidth(4f),
        strokeDashArray(new Float[]{3f, 3f})));
});
```

<center>

![Azure Maps](media/migrate-google-maps-android-app/azure-maps-polyline.png)</center> poliline

## <a name="adding-a-polygon"></a>Adicionando um polígono

Os polígonos são usados para representar uma área no mapa. Os exemplos a seguir mostram como criar um polígono que forma um triângulo com base na coordenada central do mapa.

**Antes: Google Maps**

Com o Google Maps, um polígono pode ser criado usando a classe `PolygonOptions` e adicionado ao mapa usando o método `addPolygon`. As cores de preenchimento e de traço podem ser definidas usando a opção `fillColor` e `strokeColor`, a largura do traço é definida usando a opção `strokeWidth`.

```java
@Override
public void onMapReady(GoogleMap googleMap) {
    mapView = googleMap;

    //Create the options for the polygon.
    PolygonOptions polygonOptions = new PolygonOptions()
            .add(new LatLng(46, -123))
            .add(new LatLng(49, -122))
            .add(new LatLng(46, -121))
            .add(new LatLng(46, -123))  //Close the polygon.
            .fillColor(Color.argb(128, 0, 128, 0))
            .strokeColor(Color.RED)
            .strokeWidth(5f);

    //Add the polygon to the map.
    Polygon polygon = mapView.addPolygon(polygonOptions);
}
```

<center>

![](media/migrate-google-maps-android-app/google-maps-polygon.png)</center> de polígono do Google Maps

**Após: mapas do Azure**

Nos Mapas Azure, `Polygon` e `MultiPolygon` objetos podem ser adicionados a uma fonte de dados e renderizados no mapa usando camadas. A área de um polígono pode ser renderizada em uma camada de polígono. O contorno de um polígono pode ser renderizado usando uma camada de linha. As unidades de largura de traçado e de traço "pixel" alinham-se com o Azure Maps Web SDK, na forma como a utilização dos mesmos valores em ambos os SDKs produz os mesmos resultados.

```java
mapControl.onReady(map -> {
    //Create a data source and add it to the map.
    DataSource dataSource = new DataSource();
    map.sources.add(dataSource);

    //Create an array of point arrays to create polygon rings.
    List<List<Point>> rings = Arrays.asList(Arrays.asList(
        Point.fromLngLat(-123, 46),
        Point.fromLngLat(-122, 49),
        Point.fromLngLat(-121, 46),
        Point.fromLngLat(-123, 46)));

    //Create a Polygon feature and add it to the data source.
    dataSource.add(Feature.fromGeometry(Polygon.fromLngLats(rings)));

    //Add a polygon layer for rendering the polygon area.
    map.layers.add(new PolygonLayer(dataSource,
        fillColor("green"),
        fillOpacity(0.5f)));

    //Add a line layer for rendering the polygon outline.
    map.layers.add(new LineLayer(dataSource,
        strokeColor("red"),
        strokeWidth(2f)));
});
```

<center>

![Azure Maps](media/migrate-google-maps-android-app/azure-maps-polygon.png)</center>

## <a name="overlay-a-tile-layer"></a>Sobrepor uma camada de peça

 As camadas de azulejos permitem-lhe sobrepor imagens de camadas que foram divididas em imagens de azulejos menores, que se alinham com o sistema de inclinação dos mapas. Esta abordagem é uma forma comum de sobrepor imagens de camadas ou grandes conjuntos de dados. As camadas de azulejos são conhecidas como sobreposições de imagem no Google Maps.

Os exemplos a seguir sobrepõem uma camada de bloco de radar meteorológico da Mesonet ambiental Iowa da Universidade de estado Iowa. Os blocos têm 256 pixels de tamanho.

**Antes: Google Maps**

Com o Google Maps, uma camada de peça pode ser sobreposta na parte superior do mapa use a classe `TileOverlayOptions` e adicionada ao mapa usando o método `addTileLauer`. Para tornar os azulejos semi-transparentes, a opção `transparency` está definida para 0,2, ou 20% transparente.

```java
@Override
public void onMapReady(GoogleMap googleMap) {
    mapView = googleMap;

    //Create the options for the tile layer.
    TileOverlayOptions tileLayer = new TileOverlayOptions()
        .tileProvider(new UrlTileProvider(256, 256) {
            @Override
            public URL getTileUrl(int x, int y, int zoom) {

                try {
                    //Define the URL pattern for the tile images.
                    return new URL(String.format("https://mesonet.agron.iastate.edu/cache/tile.py/1.0.0/nexrad-n0q-900913/%d/%d/%d.png", zoom, x, y));
                }catch (MalformedURLException e) {
                    throw new AssertionError(e);
                }
            }
        }).transparency(0.2f);

    //Add the tile layer to the map.
    mapView.addTileOverlay(tileLayer);
}
```

<center>

![](media/migrate-google-maps-android-app/google-maps-tile-layer.png)</center> de camada de azulejos do Google Maps

**Após: mapas do Azure**

Uma camada de azulejo pode ser adicionada ao mapa de forma semelhante a qualquer outra camada. Uma URL formatada que tem espaços reservados x, y e zoom; `{x}`, `{y}`, `{z}` respectivamente, é usado para informar à camada onde acessar os blocos. As camadas de azulejos do Azure Maps também suportam `{quadkey}`, `{bbox-epsg-3857}`e `{subdomain}` espaços reservados. Para tornar a camada de bloco semitransparente, é usado um valor de opacidade de 0,8. Opacidade e transparência, embora similares, usam valores invertidos. Para converter entre eles, subtraia o seu valor do número um.

> [!TIP]
> No Azure Maps, as camadas podem ser facilmente renderizadas abaixo de outras camadas, incluindo as camadas do mapa base. Geralmente, é desejável renderizar camadas de bloco abaixo dos rótulos de mapa para que sejam fáceis de ler. O método `map.layers.add` leva um segundo parâmetro que é o id da camada em que inserir a nova camada abaixo. Para inserir uma camada de bloco abaixo dos rótulos de mapa, o código a seguir pode ser usado: `map.layers.add(myTileLayer, "labels");`

```java
mapControl.onReady(map -> {
    //Add a tile layer to the map, below the map labels.
    map.layers.add(new TileLayer(
        tileUrl("https://mesonet.agron.iastate.edu/cache/tile.py/1.0.0/nexrad-n0q-900913/{z}/{x}/{y}.png"),
        opacity(0.8f),
        tileSize(256)
    ), "labels");
});
```

<center>

![](media/migrate-google-maps-android-app/azure-maps-tile-layer.png)</center> de camada de azulejos Do Azure Maps

## <a name="show-traffic"></a>Mostrar trânsito

Os dados de tráfego podem ser sobrepostos tanto no Azure Maps como no Google Maps.

**Antes: Google Maps**

Com o Google Maps, os dados de fluxo de tráfego podem ser sobrepostos na parte superior do mapa, passando true para o método de `setTrafficEnabled` do mapa.

```java
@Override
public void onMapReady(GoogleMap googleMap) {
    mapView = googleMap;

    mapView.setTrafficEnabled(true);
}
```

<center>

![](media/migrate-google-maps-android-app/google-maps-traffic.png)</center> de tráfego do Google Maps

**Após: mapas do Azure**

O mapas do Azure fornece várias opções diferentes para exibir o tráfego. Incidentes de tráfego, como fechamentos e acidentes de estrada, podem ser exibidos como ícones no mapa. O fluxo de tráfego, as estradas codificadas por cores, podem ser sobrepostos no mapa. As cores podem ser modificadas para aparecer em relação ao limite de velocidade registado, em relação ao atraso normal esperado, ou ao atraso absoluto. Os dados dos incidentes no Azure Maps são atualizados a cada minuto, e os dados de fluxo são atualizados a cada dois minutos.

```java
mapControl.onReady(map -> {
    map.setTraffic(
        incidents(true),
        flow(TrafficFlow.RELATIVE));
});
```

<center>

![Azure Maps](media/migrate-google-maps-android-app/azure-maps-traffic.png)</center> de trânsito

## <a name="next-steps"></a>Passos seguintes

Saiba mais sobre o SDK do Android do Azure Maps.

> [!div class="nextstepaction"]
> [Como usar o controle de mapeamento do Android](how-to-use-android-map-control-library.md)
