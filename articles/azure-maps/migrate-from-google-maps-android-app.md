---
title: 'Tutorial: Migrar uma aplicação Android Microsoft Azure Maps'
description: Como migrar uma aplicação Android do Google Maps para o Microsoft Azure Maps.
author: rbrundritt
ms.author: richbrun
ms.date: 12/17/2019
ms.topic: tutorial
ms.service: azure-maps
services: azure-maps
manager: cpendle
ms.custom: ''
ms.openlocfilehash: 9514398ec6a84becd1283e4b0975804101b64086
ms.sourcegitcommit: 0947111b263015136bca0e6ec5a8c570b3f700ff
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/24/2020
ms.locfileid: "77209737"
---
# <a name="migrate-an-android-app-from-google-maps"></a>Migrar uma aplicação Android do Google Maps

O Azure Maps Android SDK tem uma interface API semelhante ao Web SDK. Se desenvolveu com um destes SDKs, muitos dos mesmos conceitos, boas práticas e arquiteturas se aplicam.

O Azure Maps Android SDK suporta uma versão Android mínima de API 21: Android 5.0.0 (Lollipop).

Todos os exemplos são fornecidos em Java; no entanto, pode utilizar o Kotlin com o Azure Maps Android SDK.

Para obter mais informações sobre o desenvolvimento com o Android SDK by Azure Maps, consulte os [guias how-to para o Azure Maps Android SDK](how-to-use-android-map-control-library.md).

## <a name="load-a-map"></a>Carregar um mapa

Carregar um mapa numa aplicação Android utilizando o Google ou o Azure Maps consiste em passos semelhantes. Ao utilizar qualquer um dos SDK, deve:

- Obtenha uma chave API ou subscrição para aceder a qualquer uma das plataformas.
- Adicione um pouco de XML a uma Atividade para especificar onde o mapa deve ser renderizado e como deve ser definido.
- Sobrepor todos os métodos de ciclo de vida da Atividade que contém a vista do mapa para os métodos correspondentes na classe do mapa. Em particular, deve anular os seguintes métodos:
    - `onCreate(Bundle)`
    - `onStart()`
    - `onResume()`
    - `onPause()`
    - `onStop()`
    - `onDestroy()`
    - `onSaveInstanceState(Bundle)`
    - `onLowMemory()`
- Espere que o mapa esteja pronto antes de tentar aceder e programá-lo.

**Antes: Google Maps**

Para exibir um mapa utilizando o Google Maps SDK para Android, serão feitos os seguintes passos:

1.  Certifique-se de que os serviços da Google Play estão instalados.
2.  Adicione uma dependência para o serviço Do Google Maps ao ficheiro **gradle.build** do módulo: 

    `implementation 'com.google.android.gms:play-services-maps:17.0.0'`

1.  Adicione uma chave API do Google Maps dentro da secção de aplicação do ficheiro **google\_maps\_api.xml:**
    
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

1.  No ficheiro **MainActivity.java,** terá de importar o Google Maps SDK. Encaminhar todos os métodos de ciclo de vida da atividade que contém a vista do mapa para os correspondentes na classe do mapa. Recupere `MapView` uma instância do `getMapAsync(OnMapReadyCallback)` fragmento do mapa utilizando o método. O `MapView` sistema de mapas e a vista inicializam automaticamente. Editar o ficheiro **MainActivity.java** da seguinte forma:

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

Quando executa uma aplicação, o controlo do mapa carrega como na imagem seguinte.

<center>

![Mapas simples do Google](media/migrate-google-maps-android-app/simple-google-maps.png)</center>

**Depois: Mapas Azure**

Para exibir um mapa utilizando o Azure Maps SDK para Android, é necessário fazer os seguintes passos:

1. Abra o ficheiro **build.gradle** de nível superior e adicione o seguinte código à secção de todos **os projetos:**

    ```JAVA
    maven {
            url "https://atlas.microsoft.com/sdk/android"
    }
    ```

2. Atualize a sua **app/build.gradle** e adicione-lhe o seguinte código:
    
    1. Certifique-se de que o **minSdkVersion** do seu projeto está na API 21 ou superior.

    2. Adicione o seguinte código à secção Android:

        ```java
        compileOptions {
            sourceCompatibility JavaVersion.VERSION_1_8
            targetCompatibility JavaVersion.VERSION_1_8
        }
        ```
    3. Atualize o bloco de dependências. Adicione uma nova linha de dependência de implementação para o mais recente Azure Maps Android SDK:

        ```java
        implementation "com.microsoft.azure.maps:mapcontrol:0.2"
        ```

        > [!Note]
        > O Azure Maps Android SDK é regularmente atualizado e melhorado. Pode ver o [Get iniciado com o controlo](how-to-use-android-map-control-library.md) de mapas Android para obter o mais recente número da versão Do Azure Maps. Além disso, pode definir o número da versão de "0.2" para "0+" para que o seu código aponte sempre para a versão mais recente.
    
    4. Vá ao **Ficheiro** na barra de ferramentas e clique em **Sync Project com Ficheiros Gradle**.
3. Adicione um fragmento de mapa \> à \> \_atividade principal (atividade de layout de recursos principal.xml):
    
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
    
    * Importa o Azure Maps SDK
    * Detete as informações de autenticação do Seu Azure Maps
    * Obtenha a instância de controlo do mapa no método **onCreate**

     Desloque as `AzureMaps` informações `setSubscriptionKey` de `setAadProperties` autenticação na classe utilizando os ou métodos. Esta atualização global, certifique-se de que adiciona as suas informações de autenticação a todas as visualizações.

    O controlo do mapa contém os seus próprios métodos de ciclo de vida para gerir o ciclo de vida OpenGL do Android. Estes métodos devem ser chamados diretamente da atividade contida. Para chamar corretamente os métodos de ciclo de vida do controlo do mapa, deve substituir os seguintes métodos de ciclo de vida na Atividade que contém o controlo do mapa. Ligue para o respetivo método de controlo de mapas.

    * `onCreate(Bundle)` 
    * `onStart()` 
    * `onResume()` 
    * `onPause()` 
    * `onStop()` 
    * `onDestroy()` 
    * `onSaveInstanceState(Bundle)` 
    * `onLowMemory()`

    Editar o ficheiro **MainActivity.java** da seguinte forma:
    
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

Se executar a sua aplicação, o controlo do mapa carregará como na seguinte imagem.

<center>

![Mapas Azure simples](media/migrate-google-maps-android-app/simple-azure-maps.png)</center>

Note que o controlo do Azure Maps suporta a ampliação mais e proporciona mais uma visão do mundo.

> [!TIP]
> Se estiver a utilizar um emulador Android numa máquina windows, o mapa pode não render devido a conflitos com o OpenGL e a renderização gráfica acelerada do software. O seguinte tem funcionado, para algumas pessoas, para resolver esta questão. Abra o Gestor AVD e selecione o dispositivo virtual para editar. Desloque-se para baixo no painel **de configuração de verificação.** Na secção **Performance Emulada,** detete a opção **Gráficos** para **hardware**.

## <a name="localizing-the-map"></a>Localização do mapa

A localização é importante se o seu público estiver espalhado por vários países ou falar línguas diferentes.

**Antes: Google Maps**

Adicione o seguinte `onCreate` código ao método para definir a linguagem do mapa. O código deve ser adicionado antes de definir a visão de contexto do mapa. O código linguístico "fr" limita a língua ao francês.

```java
String languageToLoad = "fr";
Locale locale = new Locale(languageToLoad);
Locale.setDefault(locale);
Configuration config = new Configuration();
config.locale = locale;
getBaseContext().getResources().updateConfiguration(config,
        getBaseContext().getResources().getDisplayMetrics());
```

Aqui está um exemplo do Google Maps com o idioma definido para "fr".

<center>

![Localização do Google Maps](media/migrate-google-maps-android-app/google-maps-localization.png)</center>

**Depois: Mapas Azure**

O Azure Maps fornece três formas diferentes de definir a linguagem e a visão regional do mapa. A primeira opção é passar a informação linguística e de visão regional para a `AzureMaps` classe. Esta opção `setLanguage` utiliza `setView` a estática e os métodos a nível global. Ou seja, a linguagem padrão e a vista regional estão definidas em todos os controlos do Azure Maps carregados na sua aplicação. Este exemplo define o francês utilizando o código linguístico "fr-FR".

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

A segunda opção é passar a linguagem e ver informações para o código XML de controlo do mapa.

```xml
<com.microsoft.azure.maps.mapcontrol.MapControl
    android:id="@+id/myMap"
    android:layout_width="match_parent"
    android:layout_height="match_parent"
    app:mapcontrol_language="fr-FR"
    app:mapcontrol_view="auto"
    />
```

A terceira opção é programar a visão `setStyle` do mapa linguístico e regional usando o método dos mapas. Esta opção atualiza a linguagem e a vista regional sempre que o código executa.

```java
mapControl.onReady(map -> {
    map.setStyle(StyleOptions.language("fr-FR"));
    map.setStyle(StyleOptions.view("auto"));
});
```

Aqui está um exemplo de Azure Maps com a linguagem definida para "fr-FR".

<center>

![Localização do Azure Maps](media/migrate-google-maps-android-app/azure-maps-localization.png)</center>

Reveja a lista completa de [línguas suportadas.](supported-languages.md)

## <a name="setting-the-map-view"></a>Definindo a vista do mapa

Mapas dinâmicos tanto no Azure Maps como no Google Maps podem ser transferidos programáticamente para novos locais geográficos, chamando os métodos apropriados. Vamos fazer o mapa exibir imagens aéreas de satélite, centrar o mapa sobre um local com coordenadas, e mudar o nível de zoom. Para este exemplo, usaremos a latitude: 35.0272, longitude: -111.0225 e nível de zoom de 15.

**Antes: Google Maps**

A câmara do controlo de mapas do Google `moveCamera` Maps pode ser deslocada programáticamente utilizando o método. O `moveCamera` método permite especificar o centro do mapa e um nível de zoom. O `setMapType` método altera o tipo de mapa para visualização.

```java
@Override
public void onMapReady(GoogleMap googleMap) {
    mapView = googleMap;

    mapView.moveCamera(CameraUpdateFactory.newLatLngZoom(new LatLng(35.0272, -111.0225), 15));
    mapView.setMapType(GoogleMap.MAP_TYPE_SATELLITE);
}
```

<center>

![Vista definida do Google Maps](media/migrate-google-maps-android-app/google-maps-set-view.png)</center>

> [!NOTE]
> O Google Maps utiliza azulejos com 256 pixels em dimensões, enquanto o Azure Maps utiliza um azulejo de 512 pixels maior. Isto reduz o número de pedidos de rede necessários pelo Azure Maps para carregar a mesma área de mapas que o Google Maps. Para atingir essa mesma área visível que um mapa no Google Maps, é necessário subtrair o nível de zoom utilizado no Google Maps por um ao utilizar o Azure Maps. 

**Depois: Mapas Azure**

Como notado anteriormente, para alcançar a mesma área visível no Azure Maps subtrai o nível de zoom usado no Google Maps por um. Neste caso, utilize um nível de zoom de 14.

A vista inicial do mapa pode ser definida em atributos XML no controlo do mapa.

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

A vista do mapa pode `setCamera` ser `setStyle` programada utilizando os mapas e métodos.

```java
mapControl.onReady(map -> {
    //Set the camera of the map.
    map.setCamera(center(35.0272, -111.0225), zoom(14));

    //Set the style of the map.
    map.setStyle(style(MapStyle.SATELLITE));
});
```

<center>

![Vista definida do Azure Maps](media/migrate-google-maps-android-app/azure-maps-set-view.png)</center>

**Recursos adicionais:**

- [Estilos de mapas suportados](supported-map-styles.md)

## <a name="adding-a-marker"></a>Adicionar um marcador

Os dados de pontos são muitas vezes renderizados usando uma imagem no mapa. Estas imagens são referidas como marcadores, pinos, pinos ou símbolos. Os seguintes exemplos dão dados de pontos como marcadores no mapa em latitude: 51.5, longitude: -0.2.

**Antes: Google Maps**

Com o Google Maps, os `addMarker` marcadores são adicionados usando o método dos mapas.

```java
@Override
public void onMapReady(GoogleMap googleMap) {
    mapView = googleMap;

    mapView.addMarker(new MarkerOptions().position(new LatLng(47.64, -122.33)));
}
```

<center>

![Marcador do Google Maps](media/migrate-google-maps-android-app/google-maps-marker.png)</center>

**Depois: Mapas Azure**

No Azure Maps, verte dados de pontos no mapa adicionando primeiro os dados a uma fonte de dados. Em seguida, ligando essa fonte de dados a uma camada de símbolo. A fonte de dados otimiza a gestão de dados espaciais no controlo do mapa. A camada de símbolo especifica como renderizar dados de pontos usando como imagem ou texto.

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

![Marcador do Azure Maps](media/migrate-google-maps-android-app/azure-maps-marker.png)</center>

## <a name="adding-a-custom-marker"></a>Adicionar um marcador personalizado

Imagens personalizadas podem ser usadas para representar pontos num mapa. O mapa em exemplos abaixo usa uma imagem personalizada para exibir um ponto no mapa. O ponto é de latitude: 51,5 e longitude: -0,2. A âncora compensa a posição do marcador, de modo que o ponto do ícone do pino se alinha com a posição correta no mapa.

<center>

![imagem de pushpin amarelo](media/migrate-google-maps-web-app/ylw_pushpin.png)<br/>
ylw\_pushpin.png</center>

Em ambos os exemplos, a imagem acima é adicionada à pasta drawable dos recursos das apps.

**Antes: Google Maps**

Com o Google Maps, as imagens personalizadas podem ser usadas para marcadores. Carregue imagens personalizadas `icon` utilizando a opção do marcador. Para alinhar o ponto da imagem com `anchor` a coordenada, utilize a opção. A âncora é relativa às dimensões da imagem. Neste caso, a âncora tem 0,2 unidades de largura e 1 unidade de altura.

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

![Marcador personalizado do Google Maps](media/migrate-google-maps-android-app/google-maps-custom-marker.png)</center>

**Depois: Mapas Azure**

As camadas de símbolo no Azure Maps suportam imagens personalizadas, mas primeiro, a imagem precisa de ser carregada para os recursos do mapa e atribuiuma identificação única. Em seguida, a camada de símbolo precisa de referenciar este ID. Offset o símbolo para alinhar com o `iconOffset` ponto correto na imagem utilizando a opção. O ícone compensado está em pixels. Por padrão, a compensação é relativa ao centro inferior da imagem, mas `iconAnchor` este valor de compensação pode ser ajustado utilizando a opção. Este exemplo define `iconAnchor` a `"center"`opção para . Usa um ícone offset para mover a imagem cinco pixels para a direita e 15 pixels para alinhacom o ponto da imagem do pino.

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

![Marcador personalizado Azure Maps](media/migrate-google-maps-android-app/azure-maps-custom-marker.png)</center>

## <a name="adding-a-polyline"></a>Adicionar uma polilinha

As polilinhas são usadas para representar uma linha ou caminho no mapa. Os seguintes exemplos mostram como criar uma polilinha tracejada no mapa.

**Antes: Google Maps**

Com o Google Maps, renderiza uma polilinha usando a `PolylineOptions` classe. Adicione a polilinha ao `addPolyline` mapa utilizando o método. Despolete a `color` cor do traçado utilizando a opção. Defina a largura `width` do traçado utilizando a opção. Adicione uma matriz `pattern` de traço síptero utilizando a opção.

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

![Poliline do Google Maps](media/migrate-google-maps-android-app/google-maps-polyline.png)</center>

**Depois: Mapas Azure**

Nos Mapas Azure, os `LineString` `MultiLineString` polilines são chamados ou objetos. Adicione estes objetos a uma fonte de dados e torne-os usando uma camada de linha. Defina a largura `strokeWidth` do traçado utilizando a opção. Adicione uma matriz `strokeDashArray` de traço síptero utilizando a opção.

A largura do traçado e as unidades "pixel" do conjunto de traços no Azure Maps Web SDK, é a mesma que no serviço do Google Maps. Ambos aceitam os mesmos valores para produzir os mesmos resultados.

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

![Poliline Azure Maps](media/migrate-google-maps-android-app/azure-maps-polyline.png)</center>

## <a name="adding-a-polygon"></a>Adicionar um polígono

Os polígonos são usados para representar uma área no mapa. Os próximos exemplos mostram-lhe como criar um polígono. Este polígono forma um triângulo baseado na coordenada central do mapa.

**Antes: Google Maps**

Com o Google Maps, renderiza `PolygonOptions` um polígono usando a classe. Adicione o polígono ao `addPolygon` mapa utilizando o método. Detete as cores `fillColor` de `strokeColor` enchimento e traçado utilizando as e opções, respectivamente. Defina a largura `strokeWidth` do traçado utilizando a opção.

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

![Polígono do Google Maps](media/migrate-google-maps-android-app/google-maps-polygon.png)</center>

**Depois: Mapas Azure**

No Azure Maps, adicione `Polygon` e `MultiPolygon` objetos a uma fonte de dados e os torne no mapa usando camadas. Renderizar a área de um polígono numa camada de polígono. Descontete o contorno de um polígono usando uma camada de linha. Defina a cor e `strokeColor` `strokeWidth` a largura do traçado utilizando as opções e opções.

As unidades de largura de traçado e de traço "pixel" no Azure Maps Web SDK alinham-se com as respetivas unidades no Google Maps. Ambos aceitam os mesmos valores e produzem os mesmos resultados.

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

![Polígono do Azure Maps](media/migrate-google-maps-android-app/azure-maps-polygon.png)</center>

## <a name="overlay-a-tile-layer"></a>Sobreponha uma camada de azulejos

 Utilize camadas de azulejos para sobrepor imagens de camadas que foram divididas em imagens de azulejos menores, que se alinham com o sistema de inclinação dos mapas. Esta abordagem é uma forma comum de sobrepor imagens de camadas ou grandes conjuntos de dados. As camadas de azulejos são conhecidas como sobreposições de imagem no Google Maps.

Os seguintes exemplos sobrepõem uma camada de azulejos de radar meteorológico do Iowa Environmental Mesonet da Universidade Estadual de Iowa. Os azulejos têm 256 píxeis de tamanho.

**Antes: Google Maps**

Com o Google Maps, uma camada de azulejopode ser sobreposta em cima do mapa. Usa `TileOverlayOptions` a aula. Adicione a camada de azulejos `addTileLauer` ao mapa utilizando o método. Para tornar os azulejos `transparency` semi-transparentes, a opção está definida para 0,2, ou 20% transparente.

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

![Camada de azulejo do Google Maps](media/migrate-google-maps-android-app/google-maps-tile-layer.png)</center>

**Depois: Mapas Azure**

Uma camada de azulejo pode ser adicionada ao mapa de forma semelhante a qualquer outra camada. Um URL formatado que tem espaços reservados x, y e zoom; `{x}`, `{y}` `{z}` é utilizado, respectivamente, para dizer à camada onde aceder aos azulejos. Além disso, camadas de `{quadkey}` `{bbox-epsg-3857}`azulejos `{subdomain}` em suporte Azure Maps, e espaços reservados. Para tornar a camada de azulejosemi-transparente, é utilizado um valor de opacidade de 0,8. Opacidade e transparência, embora similares, usam valores invertidos. Para converter entre ambas as opções, subtraia o seu valor do número um.

> [!TIP]
> No Azure Maps, é conveniente renderizar camadas abaixo de outras camadas, incluindo camadas de mapas base. Além disso, é muitas vezes desejável tornar camadas de azulejos abaixo dos rótulos do mapa para que sejam fáceis de ler. O `map.layers.add` método leva um segundo parâmetro que é a identificação da camada na qual inserir a nova camada abaixo. Para inserir uma camada de azulejos abaixo das etiquetas do mapa, pode ser utilizado o seguinte código:`map.layers.add(myTileLayer, "labels");`

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

![Camada de azulejo sintetizar mapas azure](media/migrate-google-maps-android-app/azure-maps-tile-layer.png)</center>

## <a name="show-traffic"></a>Mostrar trânsito

Tanto o Azure Maps como o Google Maps têm opções para sobrepor dados de tráfego.

**Antes: Google Maps**

Com o Google Maps, os dados de fluxo de tráfego podem ser `setTrafficEnabled` sobrepostos em cima do mapa, passando para o método do mapa.

```java
@Override
public void onMapReady(GoogleMap googleMap) {
    mapView = googleMap;

    mapView.setTrafficEnabled(true);
}
```

<center>

![Tráfego do Google Maps](media/migrate-google-maps-android-app/google-maps-traffic.png)</center>

**Depois: Mapas Azure**

O Azure Maps oferece várias opções diferentes para exibir o tráfego. Incidentes de trânsito, como encerramentos de estradas e acidentes podem ser exibidos como ícones no mapa. O fluxo de tráfego e as estradas codificadas por cores podem ser sobrepostos no mapa. As cores podem ser modificadas para aparecer em relação ao limite de velocidade registado, em relação ao atraso normal esperado, ou ao atraso absoluto. Os dados dos incidentes no Azure Maps são atualizados a cada minuto, e os dados de fluxo são atualizados a cada dois minutos.

```java
mapControl.onReady(map -> {
    map.setTraffic(
        incidents(true),
        flow(TrafficFlow.RELATIVE));
});
```

<center>

![Tráfego azure Maps](media/migrate-google-maps-android-app/azure-maps-traffic.png)</center>

## <a name="next-steps"></a>Passos seguintes

Saiba mais sobre o Azure Maps Android SDK.

> [!div class="nextstepaction"]
> [Como utilizar o controlo do mapa Android](how-to-use-android-map-control-library.md)
