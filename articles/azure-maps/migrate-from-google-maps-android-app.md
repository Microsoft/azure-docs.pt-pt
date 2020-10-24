---
title: Tutorial - Migrar uma aplicação Android / Microsoft Azure Maps
description: Tutorial sobre como migrar uma aplicação Android do Google Maps para o Microsoft Azure Maps
author: rbrundritt
ms.author: richbrun
ms.date: 08/19/2020
ms.topic: tutorial
ms.service: azure-maps
services: azure-maps
manager: cpendle
ms.custom: ''
ms.openlocfilehash: d7ec1edb779ddaf53cef02dd6e83bb3e719d0b4e
ms.sourcegitcommit: 59f506857abb1ed3328fda34d37800b55159c91d
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/24/2020
ms.locfileid: "92503560"
---
# <a name="tutorial---migrate-an-android-app-from-google-maps"></a>Tutorial - Migrar uma aplicação Android do Google Maps

O Azure Maps Android SDK tem uma interface API semelhante à Web SDK. Se você desenvolveu com um destes SDKs, muitos dos mesmos conceitos, boas práticas e arquiteturas se aplicam. Neste tutorial, vai aprender a:

> [!div class="checklist"]
> * Carregue um mapa
> * Localize um mapa
> * Adicione marcadores, polilinas e polígonos.
> * Sobrepor uma camada de azulejo
> * Mostrar dados de tráfego

O Azure Maps Android SDK suporta uma versão mínima android de API 21: Android 5.0.0 (Lollipop).

Todos os exemplos são fornecidos em Java; no entanto, você pode usar Kotlin com o Azure Maps Android SDK.

Para obter mais informações sobre o desenvolvimento com o Android SDK by Azure Maps, consulte os [guias de como fazer para o Azure Maps Android SDK](how-to-use-android-map-control-library.md).

## <a name="prerequisites"></a>Pré-requisitos 

1. Inicie sessão no [portal do Azure](https://portal.azure.com). Se não tiver uma subscrição do Azure, crie uma [conta gratuita](https://azure.microsoft.com/free/) antes de começar.
2. [Faça uma conta Azure Maps](quick-demo-map-app.md#create-an-azure-maps-account)
3. [Obtenha uma chave de subscrição primária,](quick-demo-map-app.md#get-the-primary-key-for-your-account)também conhecida como a chave primária ou a chave de subscrição. Para obter mais informações sobre a autenticação no Azure Maps, consulte [a autenticação de gestão no Azure Maps.](how-to-manage-authentication.md)

## <a name="load-a-map"></a>Carregue um mapa

O carregamento de um mapa numa aplicação Android utilizando o Google ou o Azure Maps consiste em passos semelhantes. Ao utilizar qualquer SDK, deve:

* Obtenha uma chave de API ou subscrição para aceder a qualquer uma das plataformas.
* Adicione um pouco de XML a uma Atividade para especificar onde o mapa deve ser renderizado e como deve ser definido.
* Sobrepõe-se a todos os métodos do ciclo de vida da Atividade que contém a vista do mapa para os métodos correspondentes na classe do mapa. Em particular, deve anular os seguintes métodos:
    * `onCreate(Bundle)`
    * `onStart()`
    * `onResume()`
    * `onPause()`
    * `onStop()`
    * `onDestroy()`
    * `onSaveInstanceState(Bundle)`
    * `onLowMemory()`
* Aguarde que o mapa esteja pronto antes de tentar aceder e programá-lo.

### <a name="before-google-maps"></a>Antes: Google Maps

Para exibir um mapa utilizando o Google Maps SDK para Android, seriam dados os seguintes passos:

1. Certifique-se de que os serviços do Google Play estão instalados.
2. Adicione uma dependência do serviço Google Maps ao ficheiro  **gradle.build** do módulo:

    `implementation 'com.google.android.gms:play-services-maps:17.0.0'`

3. Adicione uma chave API do Google Maps dentro da secção de aplicações do google  ** \_ maps \_api.xml** ficheiro:

    ```xml
    <meta-data android:name="com.google.android.geo.API_KEY" android:value="YOUR_GOOGLE_MAPS_KEY"/>
    ```

4. Adicione um fragmento de mapa à atividade principal:

    ```xml
    <com.google.android.gms.maps.MapView
            android:id="@+id/myMap"
            android:layout_width="match_parent"
            android:layout_height="match_parent"/>
    ```

5. No ficheiro **MainActivity.java,** terá de importar o Google Maps SDK. Encaminhar todos os métodos do ciclo de vida da atividade que contém a vista do mapa para os correspondentes na classe de mapas. Recupere um `MapView` exemplo do fragmento do mapa utilizando o `getMapAsync(OnMapReadyCallback)` método. `MapView`Iniciona-se automaticamente o sistema de mapas e a vista. Editar o ficheiro **MainActivity.java** da seguinte forma:

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

Quando executam uma aplicação, o controlo do mapa carrega como na imagem seguinte.

![Mapas simples do Google](media/migrate-google-maps-android-app/simple-google-maps.png)

### <a name="after-azure-maps"></a>Depois: Mapas Azure

Para exibir um mapa utilizando o Azure Maps SDK para Android, é necessário fazer os seguintes passos:

1. Abra o ficheiro **build.gradle** de nível superior e adicione o seguinte código à secção de **blocos de todos os projetos:**

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

    3. Atualize o seu bloco de dependências. Adicione uma nova linha de dependência de implementação para o mais recente Azure Maps Android SDK:

        ```java
        implementation "com.microsoft.azure.maps:mapcontrol:0.2"
        ```

        > [!Note]
        > O Azure Maps Android SDK é regularmente atualizado e melhorado. Pode ver o [Início com o controlo de mapas Android](how-to-use-android-map-control-library.md) para obter o mais recente número de versão do Azure Maps. Além disso, pode definir o número da versão de "0.2" para "0+" para ter o seu código sempre apontado para a versão mais recente.

    4. Vá para **arquivar** na barra de ferramentas e, em seguida, clique no **Sync Project com Ficheiros Gradle**.

3. Adicione um fragmento de mapa à atividade principal (atividade de layout de recursos \> \> \_ pwdmain.xml):

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
    * Desconfie das suas informações de autenticação Azure Maps
    * Obtenha a instância de controlo do mapa no método **onCreate**

     Desa estade as informações de autenticação na `AzureMaps` classe utilizando os `setSubscriptionKey` métodos ou `setAadProperties` métodos. Esta atualização global, certifique-se de que adiciona as suas informações de autenticação a todas as vistas.

    O controlo do mapa contém os seus próprios métodos de ciclo de vida para gerir o ciclo de vida OpenGL do Android. Estes métodos devem ser chamados diretamente da Atividade contida. Para chamar corretamente os métodos de ciclo de vida do controlo do mapa, deve sobrepor-se aos seguintes métodos de ciclo de vida na Atividade que contém o controlo do mapa. Ligue para o respetivo método de controlo do mapa.

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

Se executar a sua aplicação, o controlo do mapa será carregado como na imagem seguinte.


![Mapas simples do Azure](media/migrate-google-maps-android-app/simple-azure-maps.png)

Note que o controlo do Azure Maps suporta o zoom para fora mais e fornece mais uma visão do mundo.

> [!TIP]
> Se estiver a utilizar um emulador Android numa máquina Windows, o mapa pode não renderizar devido a conflitos com a OpenGL e a renderização de gráficos acelerados por software. O que se segue tem funcionado, para algumas pessoas, para resolver esta questão. Abra o Gestor AVD e selecione o dispositivo virtual para editar. Desloque-se para baixo no painel **de configuração verificar.** Na secção **desempenho emulsionado,** deite a opção **Gráfico** para **Hardware**.

## <a name="localizing-the-map"></a>Localização do mapa

A localização é importante se o seu público estiver espalhado por vários países/regiões ou falar línguas diferentes.

### <a name="before-google-maps"></a>Antes: Google Maps

Adicione o seguinte código ao `onCreate` método para definir a linguagem do mapa. O código deve ser adicionado antes de definir a visão de contexto do mapa. O código linguístico "fr" limita a língua ao francês.

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

![Localização do Google Maps](media/migrate-google-maps-android-app/google-maps-localization.png)

### <a name="after-azure-maps"></a>Depois: Mapas Azure

O Azure Maps oferece três maneiras diferentes de definir a língua e a visão regional do mapa. A primeira opção é passar a informação de visão linguística e regional para a `AzureMaps` classe. Esta opção utiliza a estática `setLanguage` e `setView` os métodos a nível global. Ou seja, o idioma padrão e a visão regional são definidos em todos os controlos Azure Maps carregados na sua aplicação. Este exemplo define o francês utilizando o código linguístico "fr-FR".

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

A segunda opção é passar o idioma e ver informações para o código XML de controlo do mapa.

```xml
<com.microsoft.azure.maps.mapcontrol.MapControl
    android:id="@+id/myMap"
    android:layout_width="match_parent"
    android:layout_height="match_parent"
    app:mapcontrol_language="fr-FR"
    app:mapcontrol_view="auto"
    />
```

A terceira opção é programar a visão do mapa linguístico e regional usando o método dos `setStyle` mapas. Esta opção atualiza a visão linguística e regional sempre que o código é executado.

```java
mapControl.onReady(map -> {
    map.setStyle(StyleOptions.language("fr-FR"));
    map.setStyle(StyleOptions.view("auto"));
});
```

Aqui está um exemplo de Azure Maps com a linguagem definida como "fr-FR".

![Localização Azure Maps](media/migrate-google-maps-android-app/azure-maps-localization.png)

Reveja a lista completa de [línguas apoiadas.](supported-languages.md)

## <a name="setting-the-map-view"></a>Definição da vista do mapa

Mapas dinâmicos tanto no Azure Maps como no Google Maps podem ser movidos programáticamente para novas localizações geográficas, chamando os métodos apropriados. Vamos fazer o mapa exibir imagens aéreas por satélite, centrar o mapa sobre um local com coordenadas, e alterar o nível de zoom. Para este exemplo, usaremos a latitude: 35.0272, longitude: -111.0225, e nível de zoom de 15.

### <a name="before-google-maps"></a>Antes: Google Maps

A câmara do controlo de mapas do Google Maps pode ser movida programáticamente usando o `moveCamera` método. O `moveCamera` método permite especificar o centro do mapa e um nível de zoom. O `setMapType` método altera o tipo de mapa a visualizar.

```java
@Override
public void onMapReady(GoogleMap googleMap) {
    mapView = googleMap;

    mapView.moveCamera(CameraUpdateFactory.newLatLngZoom(new LatLng(35.0272, -111.0225), 15));
    mapView.setMapType(GoogleMap.MAP_TYPE_SATELLITE);
}
```

![Vista definida do Google Maps](media/migrate-google-maps-android-app/google-maps-set-view.png)

> [!NOTE]
> O Google Maps utiliza azulejos com 256 pixels em dimensões, enquanto o Azure Maps utiliza um azulejo de 512 pixels maior. Isto reduz o número de pedidos de rede necessários pelo Azure Maps para carregar a mesma área de mapa que o Google Maps. Para alcançar essa mesma área visível como um mapa no Google Maps, você precisa subtrair o nível de zoom usado no Google Maps por um quando usar O Azure Maps.

### <a name="after-azure-maps"></a>Depois: Mapas Azure

Como já foi notado, para alcançar a mesma área visível no Azure Maps subtrai o nível de zoom utilizado no Google Maps por um. Neste caso, use um nível de zoom de 14.

A visão inicial do mapa pode ser definida em atributos XML no controlo do mapa.

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

A vista do mapa pode ser programada usando os mapas `setCamera` e `setStyle` métodos.

```java
mapControl.onReady(map -> {
    //Set the camera of the map.
    map.setCamera(center(35.0272, -111.0225), zoom(14));

    //Set the style of the map.
    map.setStyle(style(MapStyle.SATELLITE));
});
```

![Vista definida Azure Maps](media/migrate-google-maps-android-app/azure-maps-set-view.png)

**Recursos adicionais:**

* [Estilos de mapas suportados](supported-map-styles.md)

## <a name="adding-a-marker"></a>Adicionar um marcador

Os dados de pontos são muitas vezes renderizados usando uma imagem no mapa. Estas imagens são referidas como marcadores, pinos, pinos ou símbolos. Os seguintes exemplos tornam os dados dos pontos como marcadores no mapa em latitude: 51.5, longitude: -0.2.

### <a name="before-google-maps"></a>Antes: Google Maps

Com o Google Maps, os marcadores são adicionados usando o método dos `addMarker` mapas.

```java
@Override
public void onMapReady(GoogleMap googleMap) {
    mapView = googleMap;

    mapView.addMarker(new MarkerOptions().position(new LatLng(47.64, -122.33)));
}
```

![Marcador do Google Maps](media/migrate-google-maps-android-app/google-maps-marker.png)

### <a name="after-azure-maps"></a>Depois: Mapas Azure

No Azure Maps, rendereja os dados do ponto no mapa adicionando primeiro os dados a uma fonte de dados. Em seguida, ligando essa fonte de dados a uma camada de símbolo. A fonte de dados otimiza a gestão de dados espaciais no controlo do mapa. A camada de símbolo especifica como fazer dados de ponto usando como imagem ou texto.

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

![Marcador de Mapas Azure](media/migrate-google-maps-android-app/azure-maps-marker.png)

## <a name="adding-a-custom-marker"></a>Adicionar um marcador personalizado

Imagens personalizadas podem ser usadas para representar pontos num mapa. O mapa em exemplos abaixo usa uma imagem personalizada para exibir um ponto no mapa. O ponto está na latitude: 51.5 e longitude: -0.2. A âncora compensa a posição do marcador, de modo que o ponto do ícone do pino de pressão se alinha com a posição correta no mapa.

<center>

![imagem de pushpin amarelo](media/migrate-google-maps-web-app/yellow-pushpin.png)<br/>
yellow-pushpin.png</center>

Em ambos os exemplos, a imagem acima é adicionada à pasta desenhável dos recursos das aplicações.

### <a name="before-google-maps"></a>Antes: Google Maps

Com o Google Maps, as imagens personalizadas podem ser usadas para marcadores. Carregue imagens personalizadas usando a opção do `icon` marcador. Para alinhar o ponto da imagem com a coordenada, utilize a `anchor` opção. A âncora é relativa às dimensões da imagem. Neste caso, a âncora tem 0,2 unidades de largura e 1 unidade de altura.

```java
@Override
public void onMapReady(GoogleMap googleMap) {
    mapView = googleMap;

    mapView.addMarker(new MarkerOptions().position(new LatLng(47.64, -122.33))
    .icon(BitmapDescriptorFactory.fromResource(R.drawable.yellow-pushpin))
    .anchor(0.2f, 1f));
}
```

![Marcador personalizado do Google Maps](media/migrate-google-maps-android-app/google-maps-custom-marker.png)

### <a name="after-azure-maps"></a>Depois: Mapas Azure

As camadas de símbolo no Azure Maps suportam imagens personalizadas, mas primeiro, a imagem precisa de ser carregada para os recursos do mapa e atribuída um ID único. Em seguida, a camada de símbolo precisa de referenciar este ID. Compense o símbolo para alinhar com o ponto correto da imagem utilizando a `iconOffset` opção. O offset do ícone está em pixels. Por predefinição, a compensação é relativa ao centro inferior da imagem, mas este valor de compensação pode ser ajustado usando a `iconAnchor` opção. Este exemplo define `iconAnchor` a opção de `"center"` . Usa um offset de ícone para mover a imagem cinco pixels para a direita e 15 pixels para se alinhar com o ponto da imagem do pino.

```java
mapControl.onReady(map -> {
    //Create a data source and add it to the map.
    DataSource dataSource = new DataSource();
    map.sources.add(dataSource);

    //Create a point feature and add it to the data source.
    dataSource.add(Feature.fromGeometry(Point.fromLngLat(-122.33, 47.64)));

    //Load the custom image icon into the map resources.
    map.images.add("my-yellow-pin", R.drawable.yellow_pushpin);

    //Create a symbol that uses the custom image icon and add it to the map.
    map.layers.add(new SymbolLayer(dataSource,
        iconImage("my-yellow-pin"),
        iconAnchor(AnchorType.CENTER
        iconOffset(new Float[]{5f, -15f})));
});
```

![Marcador personalizado Azure Maps](media/migrate-google-maps-android-app/azure-maps-custom-marker.png)

## <a name="adding-a-polyline"></a>Adicionar uma polilina

Os polilinas são usados para representar uma linha ou caminho no mapa. Os exemplos a seguir mostram como criar um polilina tracejado no mapa.

### <a name="before-google-maps"></a>Antes: Google Maps

Com o Google Maps, torne um poliline usando a `PolylineOptions` classe. Adicione o polilina ao mapa utilizando o `addPolyline` método. Desa parte da cor do traçado utilizando a `color` opção. Desa estale a largura do curso utilizando a `width` opção. Adicione uma matriz de traço de traçado utilizando a `pattern` opção.

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

![Poliline do Google Maps](media/migrate-google-maps-android-app/google-maps-polyline.png)

### <a name="after-azure-maps"></a>Depois: Mapas Azure

Nos Mapas Azure, os polilinas são chamados `LineString` ou `MultiLineString` objetos. Adicione estes objetos a uma fonte de dados e torne-os usando uma camada de linha. Desa estale a largura do curso utilizando a `strokeWidth` opção. Adicione uma matriz de traço de traçado utilizando a `strokeDashArray` opção.

A largura do traçado e as unidades "pixel" da matriz de traços no Azure Maps Web SDK, é a mesma que no serviço Google Maps. Ambos aceitam os mesmos valores para produzir os mesmos resultados.

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

![Polilina Azure Maps](media/migrate-google-maps-android-app/azure-maps-polyline.png)

## <a name="adding-a-polygon"></a>Adicionar um polígono

Os polígonos são usados para representar uma área no mapa. Os próximos exemplos mostram-lhe como criar um polígono. Este polígono forma um triângulo baseado na coordenada central do mapa.

### <a name="before-google-maps"></a>Antes: Google Maps

Com o Google Maps, renderega um polígono usando a `PolygonOptions` classe. Adicione o polígono ao mapa utilizando o `addPolygon` método. Desa estale as cores de preenchimento e de traçado utilizando as `fillColor` `strokeColor` e opções, respectivamente. Desa estale a largura do curso utilizando a `strokeWidth` opção.

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

![Polígono do Google Maps](media/migrate-google-maps-android-app/google-maps-polygon.png)

### <a name="after-azure-maps"></a>Depois: Mapas Azure

No Azure Maps, adicione `Polygon` e `MultiPolygon` objetos a uma fonte de dados e torne-os no mapa usando camadas. Torne a área de um polígono numa camada de polígono. Torne o contorno de um polígono usando uma camada de linha. Desa estale a cor e a largura do curso utilizando as `strokeColor` `strokeWidth` opções e opções.

A largura do curso e as unidades de "pixel" da matriz de traços em Azure Maps Web SDK alinham-se com as respetivas unidades no Google Maps. Ambos aceitam os mesmos valores e produzem os mesmos resultados.

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
![Polígono Azure Maps](media/migrate-google-maps-android-app/azure-maps-polygon.png)

## <a name="overlay-a-tile-layer"></a>Sobrepor uma camada de azulejo

 Use camadas de azulejos para sobrepor imagens de camadas que foram divididas em imagens de azulejos menores, que se alinham com o sistema de inclinação de mapas. Esta abordagem é uma forma comum de sobrepor imagens de camadas ou grandes conjuntos de dados. As camadas de azulejos são conhecidas como sobreposições de imagem no Google Maps.

Os exemplos a seguir sobrepõem uma camada de azulejos de radar meteorológico do Iowa Environmental Mesonet da Universidade Estadual de Iowa. Os azulejos têm 256 pixels de tamanho.

### <a name="before-google-maps"></a>Antes: Google Maps

Com o Google Maps, uma camada de azulejos pode ser sobreposta no topo do mapa. Usa a `TileOverlayOptions` aula. Adicione a camada de azulejo ao mapa utilizando o `addTileLauer` método. Para tornar os azulejos semi-transparentes, a `transparency` opção é definida em 0,2, ou 20% transparente.

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

![Camada de azulejo do Google Maps](media/migrate-google-maps-android-app/google-maps-tile-layer.png)

### <a name="after-azure-maps"></a>Depois: Mapas Azure

Uma camada de azulejo pode ser adicionada ao mapa de uma forma semelhante a qualquer outra camada. Um URL formatado que tem espaços reservados x, y e zoom; `{x}`, `{y}` `{z}` respectivamente, é utilizado para dizer a camada onde aceder aos azulejos. Além disso, camadas de azulejos no suporte Azure `{quadkey}` `{bbox-epsg-3857}` Maps, e `{subdomain}` espaços reservados. Para tornar a camada de azulejo semi-transparente, é utilizado um valor de opacidade de 0,8. Opacidade e transparência, embora semelhantes, usam valores invertidos. Para converter entre ambas as opções, subtraia o seu valor do número um.

> [!TIP]
> Em Azure Maps, é conveniente renderizar camadas abaixo de outras camadas, incluindo camadas de mapa base. Além disso, é muitas vezes desejável tornar as camadas de azulejos abaixo dos rótulos do mapa para que sejam fáceis de ler. O `map.layers.add` método requer um segundo parâmetro que é o id da camada em que inserir a nova camada abaixo. Para inserir uma camada de azulejos abaixo das etiquetas do mapa, pode utilizar-se o seguinte código: `map.layers.add(myTileLayer, "labels");`

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

![Camada de azulejos Azure Maps](media/migrate-google-maps-android-app/azure-maps-tile-layer.png)

## <a name="show-traffic"></a>Mostrar trânsito

Tanto o Azure Maps como o Google Maps têm opções para sobrepor dados de tráfego.

### <a name="before-google-maps"></a>Antes: Google Maps

Com o Google Maps, os dados de fluxo de tráfego podem ser sobrepostos no topo do mapa, passando fiel ao método do `setTrafficEnabled` mapa.

```java
@Override
public void onMapReady(GoogleMap googleMap) {
    mapView = googleMap;

    mapView.setTrafficEnabled(true);
}
```

![Tráfego do Google Maps](media/migrate-google-maps-android-app/google-maps-traffic.png)

### <a name="after-azure-maps"></a>Depois: Mapas Azure

O Azure Maps oferece várias opções diferentes para exibir tráfego. Incidentes de trânsito, como encerramentos de estradas e acidentes, podem ser exibidos como ícones no mapa. O fluxo de tráfego e as estradas codificadas por cores podem ser sobrepostas no mapa. As cores podem ser modificadas para aparecerem em relação ao limite de velocidade registado, em relação ao atraso normal esperado, ou ao atraso absoluto. Os dados do incidente no Azure Maps são atualizados a cada minuto e os dados de fluxo são atualizados a cada dois minutos.

```java
mapControl.onReady(map -> {
    map.setTraffic(
        incidents(true),
        flow(TrafficFlow.RELATIVE));
});
```

![Tráfego Azure Maps](media/migrate-google-maps-android-app/azure-maps-traffic.png)

## <a name="next-steps"></a>Passos seguintes

Saiba mais sobre o Azure Maps Android SDK:

> [!div class="nextstepaction"]
> [Como utilizar o controlo do mapa Android](how-to-use-android-map-control-library.md)

> [!div class="nextstepaction"]
> [Adicione uma camada de símbolo a um mapa Android](how-to-add-symbol-to-android-map.md)

> [!div class="nextstepaction"]
> [Adicione formas a um mapa Android](https://docs.microsoft.com/azure/azure-maps/how-to-add-shapes-to-android-map)

> [!div class="nextstepaction"]
> [Alterar estilos de mapa em mapas Android](https://docs.microsoft.com/azure/azure-maps/set-android-map-styles)