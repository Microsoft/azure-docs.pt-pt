---
title: Migrar um aplicativo do Android | Microsoft Docs
description: Um tutorial sobre como migrar um aplicativo Android do Google Maps para mapas de Microsoft Azure.
author: rbrundritt
ms.author: richbrun
ms.date: 12/17/2019
ms.topic: conceptual
ms.service: azure-maps
services: azure-maps
manager: cpendle
ms.custom: ''
ms.openlocfilehash: 60d8fcc9879e89276aad80bbaf3a0edf244a45b8
ms.sourcegitcommit: f4f626d6e92174086c530ed9bf3ccbe058639081
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 12/25/2019
ms.locfileid: "75481402"
---
# <a name="migrate-an-android-app-from-google-maps"></a>Migrar um aplicativo Android do Google Maps

O Azure Maps SDK do Android tem uma interface de API que é muito semelhante ao SDK da Web. Se você tiver desenvolvido com um desses SDKs, muitos dos mesmos conceitos, práticas recomendadas e arquiteturas se aplicam, e você deve ser capaz de transferir facilmente seu conhecimento de um para o outro.

O Azure Maps SDK do Android dá suporte a uma versão mínima do Android da API 21: Android 5.0.0 (pirulito).

Todos os exemplos fornecidos no Java, no entanto, Kotlin também podem ser usados com o SDK do Android do Azure Maps.

Consulte também os [guias de instruções para o Azure Maps SDK do Android](how-to-use-android-map-control-library.md) para obter mais informações sobre como desenvolver com este SDK.

## <a name="load-a-map"></a>Carregar um mapa

Carregar um mapa em um aplicativo Android usando o Google ou o Azure Maps consiste em muitas das mesmas etapas. Ao usar o SDK, você deve:

- Obtenha uma API ou chave de assinatura para acessar qualquer uma das plataformas.
- Adicione um XML a uma atividade para especificar onde o mapa deve ser renderizado e como ele deve ser disposto.
- Encaminhe todos os métodos de ciclo de vida da atividade que contém a exibição de mapa para os correspondentes na classe de mapa. Em particular, você deve encaminhar os seguintes métodos:
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

1.  Adicione uma chave de API do Google Maps dentro da seção do aplicativo do arquivo **API. XML do\_mapas\_do Google** :
    
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

1.  No arquivo **MainActivity. java** , você precisará adicionar importações para o SDK do Google Maps. Encaminhe todos os métodos de ciclo de vida da atividade que contém a exibição de mapa para os correspondentes na classe de mapa. Uma instância de `MapView` pode ser recuperada do fragmento do mapa usando o método `getMapAsync(OnMapReadyCallback)`. O `MapView` inicializa automaticamente o sistema de mapas e a exibição. Edite o arquivo **MainActivity. java** da seguinte maneira:

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

Quando executado em um aplicativo, o controle de mapa será carregado da seguinte maneira.

<center>

![](media/migrate-google-maps-android-app/simple-google-maps.png)</center> simples do Google Maps

**Após: mapas do Azure**

Para exibir um mapa usando o SDK do Azure Maps para Android, as etapas a seguir precisam ser feitas:

1. Abra o arquivo **Build. gradle** de nível superior e adicione o seguinte código à seção **todos os projetos**, blocos de **repositórios** :

    ```
    maven {
            url "https://atlas.microsoft.com/sdk/android"
    }
    ```

2. Atualize seu **app/Build. gradle** e adicione o seguinte código a ele:
    
    1. Verifique se o **minSdkVersion** do seu projeto está na API 21 ou superior.

    2. Adicione o seguinte código à seção do Android:

        ```
        compileOptions {
            sourceCompatibility JavaVersion.VERSION_1_8
            targetCompatibility JavaVersion.VERSION_1_8
        }
        ```
    3. Atualize seu bloco de dependências e adicione uma nova linha de dependência de implementação para os mais recentes SDK do Android do Azure Maps:

        ```
        implementation "com.microsoft.azure.maps:mapcontrol:0.2"
        ```

        > [!Note]
        > O Azure Maps SDK do Android está sendo atualizado e aprimorado regularmente. Você pode ver a documentação [introdução ao controle de mapa do Android](how-to-use-android-map-control-library.md) para obter o número de versão mais recente da implementação do Azure Maps. Além disso, você pode definir o número de versão de "0,2" como "0 +" para que ele sempre aponte para a versão mais recente.
    
    4. Vá para **arquivo** na barra de ferramentas e clique em **sincronizar projeto com arquivos gradle**.
3. Adicione um fragmento de mapa à atividade principal (res \> layout \> atividade\_Main. xml):
    
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

4. No arquivo **MainActivity. java** , você precisará:
    
    * Adicionar importações para o SDK do Azure Maps
    * definir as informações de autenticação do Azure Maps
    * obter a instância de controle de mapa no método **OnCreate**

    Definir as informações de autenticação na classe `AzureMaps` globalmente usando os métodos `setSubscriptionKey` ou `setAadProperties` faz isso para que você não precise adicionar suas informações de autenticação a cada exibição. 

    O controle de mapa contém seus próprios métodos de ciclo de vida para gerenciar o ciclo de vida do OpenGL do Android, que deve ser chamado diretamente da atividade que o contém. Para que o aplicativo chame corretamente os métodos de ciclo de vida do controle de mapa, você deve substituir os seguintes métodos de ciclo de vida na atividade que contém o controle de mapa e chamar o respectivo método de controle de mapa. 

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

![mapas simples do Azure](media/migrate-google-maps-android-app/simple-azure-maps.png)</center>

Observe que o controle de mapas do Azure dá suporte à ampliação de mais e fornece uma exibição mundial.

> [!TIP]
> Se estiver usando um emulador do Android no Windows, o mapa poderá não ser renderizado devido a conflitos com o OpenGL e a renderização de gráficos acelerada por software. O seguinte procedimento funcionou para resolver esse problema. Abra o Gerenciador de AVD e selecione o dispositivo virtual a ser editado. Na seção **desempenho emulado** , defina a opção **gráficos** como **hardware**.

## <a name="localizing-the-map"></a>Localizando o mapa

Se seu público estiver espalhado em vários países ou falar em idiomas diferentes, a localização será importante.

**Antes: Google Maps**

O idioma do mapa pode ser definido no método `onCreate` da atividade principal, adicionando o código a seguir antes de definir a exibição de contexto do mapa. O seguinte limita o idioma ao francês usando o código de idioma "fr".

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

![localização do Google Maps](media/migrate-google-maps-android-app/google-maps-localization.png)</center>

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

A terceira opção é definir programaticamente o idioma e a exibição regional do mapa usando o método Maps `setStyle`. Isso pode ser feito a qualquer momento para alterar o idioma e a exibição regional do mapa.

```java
mapControl.onReady(map -> {
    map.setStyle(StyleOptions.language("fr-FR"));
    map.setStyle(StyleOptions.view("auto"));
});
```

Aqui está um exemplo de mapas do Azure com o idioma definido como "fr-FR".

<center>

![a localização do Azure Maps](media/migrate-google-maps-android-app/azure-maps-localization.png)</center>

Uma lista completa de idiomas com suporte e exibições regionais está documentada [aqui](supported-languages.md).

## <a name="setting-the-map-view"></a>Configurando a exibição do mapa

Mapas dinâmicos no Azure e no Google Maps podem ser movidos programaticamente para novos locais geográficos chamando os métodos apropriados. Os exemplos a seguir mostram como fazer com que o mapa exiba imagens aéreas satélite, centralize o mapa em um local com coordenadas (Latitude: 35, 272, Longitude:-111, 225) e altere o nível de zoom para 15 no Google Maps.

> [!NOTE]
> O Google Maps usa blocos que são 256 pixels em dimensões enquanto o Azure Maps usa um bloco maior de 512 pixels. Isso reduz o número de solicitações de rede necessárias ao mapas do Azure para carregar a mesma área do mapa que o Google Maps. No entanto, devido à maneira como as pirâmides de bloco funcionam nos controles de mapa, os blocos maiores no Azure Maps significam que para alcançar essa mesma área visível como um mapa no Google Maps, você precisa subtrair o nível de zoom usado no Google Maps em 1 ao usar mapas do Azure.

**Antes: Google Maps**

A câmera do controle de mapa do Google Maps pode ser movida de forma programática usando o método `moveCamera`, que permite especificar o centro do mapa e um nível de zoom. O método `setMapType` pode ser usado para alterar o tipo de mapa exibido.

```java
@Override
public void onMapReady(GoogleMap googleMap) {
    mapView = googleMap;

    mapView.moveCamera(CameraUpdateFactory.newLatLngZoom(new LatLng(35.0272, -111.0225), 15));
    mapView.setMapType(GoogleMap.MAP_TYPE_SATELLITE);
}
```

<center>

![modo de exibição de conjunto do Google Maps](media/migrate-google-maps-android-app/google-maps-set-view.png)</center>

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

![exibição de conjunto de mapas do Azure](media/migrate-google-maps-android-app/azure-maps-set-view.png)</center>

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

![](media/migrate-google-maps-android-app/google-maps-marker.png)</center> de marcador do Google Maps

**Após: mapas do Azure**

No Azure Maps, os dados podem ser renderizados no mapa, primeiro adicionando os dados a uma fonte de dados e, em seguida, conectando essa fonte de dados a uma camada de símbolo. A fonte de dados otimiza o gerenciamento de dados espaciais no controle de mapa e a camada de símbolo especifica como renderizar dados de ponto usando como uma imagem e/ou texto.

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

![](media/migrate-google-maps-android-app/azure-maps-marker.png)</center> de marcador do Azure Maps

## <a name="adding-a-custom-marker"></a>Adicionando um marcador personalizado

Imagens personalizadas podem ser usadas para representar pontos em um mapa. A imagem a seguir é usada nos exemplos abaixo, use uma imagem personalizada para exibir um ponto no mapa em (Latitude: 51,5, Longitude:-0,2) e desloca a posição do marcador para que o ponto do ícone de pino fique alinhado com a posição correta no mapa.

<center>

![imagem de pino amarelo](media/migrate-google-maps-web-app/ylw_pushpin.png)<br/>
YLW\_</center> de anotações. png

Em ambos os exemplos, a imagem acima é adicionada à pasta desenhável dos recursos de aplicativos.

**Antes: Google Maps**

Com o Google Maps, as imagens personalizadas podem ser usadas para marcadores carregando-as por meio da opção de `icon` do marcador. Para alinhar o ponto da imagem com a coordenada, a opção `anchor` pode ser usada. A âncora é relativa às dimensões da imagem, neste caso, 0,2 unidades de largura e 1 unidade alta.

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

![marcador personalizado do Google Maps](media/migrate-google-maps-android-app/google-maps-custom-marker.png)</center>

**Após: mapas do Azure**

As camadas de símbolo no Azure Maps também dão suporte a imagens personalizadas, mas a imagem precisa ser carregada nos recursos de mapa primeiro e atribuída uma ID exclusiva. Em seguida, a camada de símbolo pode fazer referência a essa ID. O símbolo pode ser deslocado para alinhar-se ao ponto correto na imagem usando a opção `iconOffset`. Observe que o deslocamento do ícone é em pixels. Por padrão, o deslocamento é relativo ao centro inferior da imagem, mas isso pode ser ajustado usando a opção `iconAnchor`. Este exemplo define a opção `iconAnchor` como `"center"` e usa um deslocamento de ícone para mover a imagem em cinco pixels para a direita e 15 pixels para alinhar com o ponto da imagem do pino.

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

![marcador personalizado do Azure Maps](media/migrate-google-maps-android-app/azure-maps-custom-marker.png)</center>

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

![](media/migrate-google-maps-android-app/google-maps-polyline.png)</center> de polilinha do Google Maps

**Após: mapas do Azure**

No Azure Maps, as polilinhas são chamadas de objetos LineString ou MultiLineString. Esses objetos podem ser adicionados a uma fonte de dados e renderizados usando uma camada de linha. Observe que as unidades de "pixel" da largura do traço e da matriz Dash se alinham com o SDK da Web do Azure Maps no que usa os mesmos valores em ambos os SDKs produzem os mesmos resultados.

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

![](media/migrate-google-maps-android-app/azure-maps-polyline.png)</center> de polilinha do Azure Maps

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

![polígono do Google Maps](media/migrate-google-maps-android-app/google-maps-polygon.png)</center>

**Após: mapas do Azure**

Nos mapas do Azure, os objetos Polygon e MultiPolygon podem ser adicionados a uma fonte de dados e renderizados no mapa usando camadas. A área de um polígono pode ser renderizada em uma camada de polígono. O contorno de um polígono pode ser renderizado usando uma camada de linha. Observe que as unidades de "pixel" da largura do traço e da matriz Dash se alinham com o SDK da Web do Azure Maps no que usa os mesmos valores em ambos os SDKs produzem os mesmos resultados.

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

![polígono do Azure Maps](media/migrate-google-maps-android-app/azure-maps-polygon.png)</center>

## <a name="overlay-a-tile-layer"></a>Sobrepor uma camada de peça

As camadas de bloco, também conhecidas como sobreposições de imagem no Google Maps, permitem que você sobreponha as imagens de camada que foram divididas em imagens com blocos menores que se alinham com o sistema de mosaico de mapas. Essa é uma maneira comum de sobrepor imagens de camada ou conjuntos de dados muito grandes.

Os exemplos a seguir sobrepõem uma camada de bloco de radar meteorológico da Mesonet ambiental Iowa da Universidade de estado Iowa. Os blocos têm 256 pixels de tamanho.

**Antes: Google Maps**

Com o Google Maps, uma camada de peça pode ser sobreposta na parte superior do mapa use a classe `TileOverlayOptions` e adicionada ao mapa usando o método `addTileLauer`. Para tornar os blocos semitransparente, a opção `transparency` é definida como 0,2 ou 20% transparente.

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

![camada de bloco do Google Maps](media/migrate-google-maps-android-app/google-maps-tile-layer.png)</center>

**Após: mapas do Azure**

No Azure Maps, uma camada de peça pode ser adicionada ao mapa praticamente da mesma forma que qualquer outra camada. Uma URL formatada que tem espaços reservados x, y e zoom; `{x}`, `{y}`, `{z}` respectivamente, é usado para informar à camada onde acessar os blocos. As camadas de bloco no Azure Maps também oferecem suporte a espaços reservados `{quadkey}`, `{bbox-epsg-3857}` e `{subdomain}`. Para tornar a camada de bloco semitransparente, é usado um valor de opacidade de 0,8. Observe que a opacidade e a transparência, embora semelhantes, usam valores invertidos. Para converter entre eles, basta subtrair seu valor do número um.

> [!TIP]
> No Azure Maps, as camadas podem ser facilmente renderizadas abaixo de outras camadas, incluindo as camadas do mapa base. Geralmente, é desejável renderizar camadas de bloco abaixo dos rótulos de mapa para que sejam fáceis de ler. O método `map.layers.add` usa um segundo parâmetro que é a ID da camada na qual inserir a nova camada abaixo. Para inserir uma camada de bloco abaixo dos rótulos de mapa, o código a seguir pode ser usado: `map.layers.add(myTileLayer, "labels");`

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

camada de bloco do ![mapas do Azure](media/migrate-google-maps-android-app/azure-maps-tile-layer.png)</center>

## <a name="show-traffic"></a>Mostrar trânsito

Os dados de tráfego podem ser sobrepostos ao Azure e ao Google Maps.

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

![o tráfego do Google Maps](media/migrate-google-maps-android-app/google-maps-traffic.png)</center>

**Após: mapas do Azure**

O mapas do Azure fornece várias opções diferentes para exibir o tráfego. Incidentes de tráfego, como fechamentos e acidentes de estrada, podem ser exibidos como ícones no mapa. O fluxo de tráfego, as estradas codificadas por cor, podem ser sobrepostas no mapa e as cores podem ser modificadas para serem baseadas em relação ao limite de velocidade lançado, em relação ao atraso normal esperado ou ao atraso absoluto. Os dados de incidentes no Azure Maps são atualizados a cada minuto e fluem os dados a cada dois minutos.

```java
mapControl.onReady(map -> {
    map.setTraffic(
        incidents(true),
        flow(TrafficFlow.RELATIVE));
});
```

<center>

![o tráfego do Azure Maps](media/migrate-google-maps-android-app/azure-maps-traffic.png)</center>

## <a name="next-steps"></a>Passos seguintes

Saiba mais sobre o SDK do Android do Azure Maps.

> [!div class="nextstepaction"]
> [Como usar o controle de mapeamento do Android](how-to-use-android-map-control-library.md)
