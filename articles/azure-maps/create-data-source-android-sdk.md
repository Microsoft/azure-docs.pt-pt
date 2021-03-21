---
title: Crie uma fonte de dados para mapas Android | Microsoft Azure Maps
description: 'Descubra como criar uma fonte de dados para um mapa. Conheça as fontes de dados que o Azure Maps Android SDK utiliza: fontes geoJSON e azulejos vetoriais.'
author: rbrundritt
ms.author: richbrun
ms.date: 2/26/2021
ms.topic: conceptual
ms.service: azure-maps
services: azure-maps
manager: cpendle
zone_pivot_groups: azure-maps-android
ms.openlocfilehash: e870134e2ecd431aa3e5c02638120027f0d47df2
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/20/2021
ms.locfileid: "102101465"
---
# <a name="create-a-data-source-android-sdk"></a>Criar uma fonte de dados (Android SDK)

O Azure Maps Android SDK armazena dados em fontes de dados. A utilização de fontes de dados otimiza as operações de dados para consulta e renderização. Atualmente existem dois tipos de fontes de dados:

- **GeoJSON fonte**: Gere dados de localização bruta em formato GeoJSON localmente. Bom para pequenos a médios conjuntos de dados (mais de centenas de milhares de formas).
- **Fonte de azulejos vetoriais**: Carrega dados formatados como azulejos vetoriais para a visão do mapa atual, com base no sistema de inclinação de mapas. Ideal para grandes e maciços conjuntos de dados (milhões ou biliões de formas).

## <a name="geojson-data-source"></a>Fonte de dados da GeoJSON

A Azure Maps utiliza a GeoJSON como um dos seus modelos de dados primários. GeoJSON é uma forma geoespacial aberta de representar dados geoespaciais em formato JSON. As aulas de GeoJSON disponíveis no Azure Maps Android SDK para facilitar a criação e serializem os dados da GeoJSON. Carregue e guarde os dados da GeoJSON na `DataSource` classe e torne-os usando camadas. O código que se segue mostra como os objetos GeoJSON podem ser criados no Azure Maps.

::: zone pivot="programming-language-java-android"

```java
/*
    Raw GeoJSON feature
    
    {
         "type": "Feature",
         "geometry": {
             "type": "Point",
             "coordinates": [-100, 45]
         },
         "properties": {
             "custom-property": "value"
         }
    }

*/

//Create a point feature.
Feature feature = Feature.fromGeometry(Point.fromLngLat(-100, 45));

//Add a property to the feature.
feature.addStringProperty("custom-property", "value");

//Add the feature to the data source.
source.add(feature);
```

::: zone-end

::: zone pivot="programming-language-kotlin"

```kotlin
/*
    Raw GeoJSON feature
    
    {
         "type": "Feature",
         "geometry": {
             "type": "Point",
             "coordinates": [-100, 45]
         },
         "properties": {
             "custom-property": "value"
         }
    }

*/

//Create a point feature.
val feature = Feature.fromGeometry(Point.fromLngLat(-100, 45))

//Add a property to the feature.
feature.addStringProperty("custom-property", "value")

//Add the feature to the data source.
source.add(feature)
```

::: zone-end

Alternativamente, as propriedades podem ser carregadas num JsonObject primeiro e depois passadas para a funcionalidade ao criá-la, como mostrado abaixo.

::: zone pivot="programming-language-java-android"

```java
//Create a JsonObject to store properties for the feature.
JsonObject properties = new JsonObject();
properties.addProperty("custom-property", "value");

Feature feature = Feature.fromGeometry(Point.fromLngLat(-100, 45), properties);
```

::: zone-end

::: zone pivot="programming-language-kotlin"

```kotlin
//Create a JsonObject to store properties for the feature.
val properties = JsonObject()
properties.addProperty("custom-property", "value")

val feature = Feature.fromGeometry(Point.fromLngLat(-100, 45), properties)
```

::: zone-end

Uma vez criada uma funcionalidade GeoJSON, uma fonte de dados pode ser adicionada ao mapa através `sources` da propriedade do mapa. O código que se segue mostra como criar um `DataSource` , adicioná-lo ao mapa, e adicionar uma funcionalidade à fonte de dados.

```javascript
//Create a data source and add it to the map.
DataSource source = new DataSource();
map.sources.add(source);

//Add GeoJSON feature to the data source.
source.add(feature);
```

O código que se segue mostra várias formas de criar uma funcionalidade GeoJSON, FuncionalidadeCollection e geometrias.

::: zone pivot="programming-language-java-android"

```java
//GeoJSON Point Geometry
Point point = Point.fromLngLat(LONGITUDE, LATITUDE);

//GeoJSON Point Geometry
LineString linestring = LineString.fromLngLats(PointList);

//GeoJSON Polygon Geometry
Polygon polygon = Polygon.fromLngLats(listOfPointList);
 
Polygon polygonFromOuterInner = Polygon.fromOuterInner(outerLineStringObject,innerLineStringObject);

//GeoJSON MultiPoint Geometry
MultiPoint multiPoint = MultiPoint.fromLngLats(PointList);

//GeoJSON MultiLineString Geometry
MultiLineString multiLineStringFromLngLat = MultiLineString.fromLngLats(listOfPointList);

MultiLineString multiLineString = MultiLineString.fromLineString(singleLineString);

//GeoJSON MultiPolygon Geometry
MultiPolygon multiPolygon = MultiPolygon.fromLngLats(listOflistOfPointList);

MultiPolygon multiPolygonFromPolygon = MultiPolygon.fromPolygon(polygon);

MultiPolygon multiPolygonFromPolygons = MultiPolygon.fromPolygons(PolygonList);

//GeoJSON Feature
Feature pointFeature = Feature.fromGeometry(Point.fromLngLat(LONGITUDE, LATITUDE));
 
//GeoJSON FeatureCollection 
FeatureCollection featureCollectionFromSingleFeature = FeatureCollection.fromFeature(pointFeature);
 
FeatureCollection featureCollection = FeatureCollection.fromFeatures(listOfFeatures);
```

::: zone-end

::: zone pivot="programming-language-kotlin"

```kotlin
//GeoJSON Point Geometry
val point = Point.fromLngLat(LONGITUDE, LATITUDE)

//GeoJSON Point Geometry
val linestring = LineString.fromLngLats(PointList)

//GeoJSON Polygon Geometry
val polygon = Polygon.fromLngLats(listOfPointList)

val polygonFromOuterInner = Polygon.fromOuterInner(outerLineStringObject, innerLineStringObject)

//GeoJSON MultiPoint Geometry
val multiPoint = MultiPoint.fromLngLats(PointList)

//GeoJSON MultiLineString Geometry
val multiLineStringFromLngLat = MultiLineString.fromLngLats(listOfPointList)

val multiLineString = MultiLineString.fromLineString(singleLineString)

//GeoJSON MultiPolygon Geometry
val multiPolygon = MultiPolygon.fromLngLats(listOflistOfPointList)

val multiPolygonFromPolygon = MultiPolygon.fromPolygon(polygon)

val multiPolygonFromPolygons = MultiPolygon.fromPolygons(PolygonList)

//GeoJSON Feature
val pointFeature = Feature.fromGeometry(Point.fromLngLat(LONGITUDE, LATITUDE))

//GeoJSON FeatureCollection 
val featureCollectionFromSingleFeature = FeatureCollection.fromFeature(pointFeature)

val featureCollection = FeatureCollection.fromFeatures(listOfFeatures)
```

::: zone-end

### <a name="serialize-and-deserialize-geojson"></a>Serializar e deserializar GeoJSON

As classes de coleção, características e geometria têm todos `fromJson()` `toJson()` métodos estáticos, que ajudam na serialização. A corda JSON válida em formatada passou através do `fromJson()` método irá criar o objeto de geometria. Este `fromJson()` método também significa que você pode usar Gson ou outras estratégias de serialização/deserialização. O código que se segue mostra como pegar numa funcionalidade de GeoJSON e deserializá-la para a classe Feature e, em seguida, serializá-la de volta para uma cadeia GeoJSON.

::: zone pivot="programming-language-java-android"

```java
//Take a stringified GeoJSON object.
String GeoJSON_STRING = "{"
    + "      \"type\": \"Feature\","            
    + "      \"geometry\": {"
    + "            \"type\": \"Point\""
    + "            \"coordinates\": [-100, 45]"
    + "      },"
    + "      \"properties\": {"
    + "            \"custom-property\": \"value\""
    + "      },"
    + "}";

//Deserialize the JSON string into a feature.
Feature feature = Feature.fromJson(GeoJSON_STRING);
 
//Serialize a feature collection to a string.
String featureString = feature.toJson();
```

::: zone-end

::: zone pivot="programming-language-kotlin"

```kotlin
//Take a stringified GeoJSON object.
val GeoJSON_STRING = ("{"
        + "      \"type\": \"Feature\","
        + "      \"geometry\": {"
        + "            \"type\": \"Point\""
        + "            \"coordinates\": [-100, 45]"
        + "      },"
        + "      \"properties\": {"
        + "            \"custom-property\": \"value\""
        + "      },"
        + "}")

//Deserialize the JSON string into a feature.
val feature = Feature.fromJson(GeoJSON_STRING)

//Serialize a feature collection to a string.
val featureString = feature.toJson()
```

::: zone-end

### <a name="import-geojson-data-from-web-or-assets-folder"></a>Importar dados da GeoJSON a partir de pasta web ou ativos

A maioria dos ficheiros GeoJSON contêm uma FuncionalidadeCollection. Leia os ficheiros GeoJSON como cordas e use o `FeatureCollection.fromJson` método para desercializá-lo.

O código a seguir é uma classe reutilizável para importar dados da pasta web ou dos ativos locais como uma cadeia e devolvê-lo à linha de UI através de uma função de retorno.

::: zone pivot="programming-language-java-android"

```java
import android.content.Context;
import android.os.Handler;
import android.os.Looper;
import android.webkit.URLUtil;

import java.io.BufferedReader;
import java.io.IOException;
import java.io.InputStream;
import java.io.InputStreamReader;
import java.net.URL;
import java.util.concurrent.ExecutorService;
import java.util.concurrent.Executors;

import javax.net.ssl.HttpsURLConnection;

public class Utils {

    interface SimpleCallback {
        void notify(String result);
    }

    /**
     * Imports data from a web url or asset file name and returns it to a callback.
     * @param urlOrFileName A web url or asset file name that points to data to load.
     * @param context The context of the app.
     * @param callback The callback function to return the data to.
     */
    public static void importData(String urlOrFileName, Context context, SimpleCallback callback){
        importData(urlOrFileName, context, callback, null);
    }
    
    /**
     * Imports data from a web url or asset file name and returns it to a callback.
     * @param urlOrFileName A web url or asset file name that points to data to load.
     * @param context The context of the app.
     * @param callback The callback function to return the data to.
     * @param error A callback function to return errors to.
     */
    public static void importData(String urlOrFileName, Context context, SimpleCallback callback, SimpleCallback error){
        if(urlOrFileName != null && callback != null) {
            ExecutorService executor = Executors.newSingleThreadExecutor();
            Handler handler = new Handler(Looper.getMainLooper());

            executor.execute(() -> {
                String data = null;

                try {

                    if(URLUtil.isNetworkUrl(urlOrFileName)){
                        data = importFromWeb(urlOrFileName);
                    } else {
                        //Assume file is in assets folder.
                        data = importFromAssets(context, urlOrFileName);
                    }

                    final String result = data;

                    handler.post(() -> {
                        //Ensure the resulting data string is not null or empty.
                        if (result != null && !result.isEmpty()) {
                            callback.notify(result);
                        } else {
                            error.notify("No data imported.");
                        }
                    });
                } catch(Exception e) {
                    if(error != null){
                        error.notify(e.getMessage());
                    }
                }
            });
        }
    }

    /**
     * Imports data from an assets file as a string.
     * @param context The context of the app.
     * @param fileName The asset file name.
     * @return
     * @throws IOException
     */
    private static String importFromAssets(Context context, String fileName) throws IOException {
        InputStream stream = null;

        try {
            stream = context.getAssets().open(fileName);

            if(stream != null) {
                return readStreamAsString(stream);
            }
        } catch (Exception e) {
            e.printStackTrace();
        } finally {
            // Close Stream and disconnect HTTPS connection.
            if (stream != null) {
                stream.close();
            }
        }

        return null;
    }

    /**
     * Imports data from the web as a string.
     * @param url URL to the data.
     * @return
     * @throws IOException
     */
    private static String importFromWeb(String url) throws IOException {
        InputStream stream = null;
        HttpsURLConnection connection = null;
        String result = null;

        try {
            connection = (HttpsURLConnection) new URL(url).openConnection();

            //For this use case, set HTTP method to GET.
            connection.setRequestMethod("GET");

            //Open communications link (network traffic occurs here).
            connection.connect();

            int responseCode = connection.getResponseCode();
            if (responseCode != HttpsURLConnection.HTTP_OK) {
                throw new IOException("HTTP error code: " + responseCode);
            }

            //Retrieve the response body as an InputStream.
            stream = connection.getInputStream();

            if (stream != null) {
                return readStreamAsString(stream);
            }
        } catch (Exception e) {
            e.printStackTrace();
        } finally {
            // Close Stream and disconnect HTTPS connection.
            if (stream != null) {
                stream.close();
            }
            if (connection != null) {
                connection.disconnect();
            }
        }

        return result;
    }

    /**
     * Reads an input stream as a string.
     * @param stream Stream to convert.
     * @return
     * @throws IOException
     */
    private static String readStreamAsString(InputStream stream) throws IOException {
        //Convert the contents of an InputStream to a String.
        BufferedReader in = new BufferedReader(new InputStreamReader(stream, "UTF-8"));

        String inputLine;
        StringBuffer response = new StringBuffer();

        while ((inputLine = in.readLine()) != null) {
            response.append(inputLine);
        }

        in.close();

        return response.toString();
    }
}
```

::: zone-end

::: zone pivot="programming-language-kotlin"

```kotlin
import android.content.Context
import android.os.Handler
import android.os.Looper
import android.webkit.URLUtil
import java.net.URL
import java.util.concurrent.ExecutorService
import java.util.concurrent.Executors

class Utils {
    companion object {

        /**
            * Imports data from a web url or asset file name and returns it to a callback.
            * @param urlOrFileName A web url or asset file name that points to data to load.
            * @param context The context of the app.
            * @param callback The callback function to return the data to.
            */
        fun importData(urlOrFileName: String?, context: Context, callback: (String?) -> Unit) {
            importData(urlOrFileName, context, callback, null)
        }

        /**
            * Imports data from a web url or asset file name and returns it to a callback.
            * @param urlOrFileName A web url or asset file name that points to data to load.
            * @param context The context of the app.
            * @param callback The callback function to return the data to.
            * @param error A callback function to return errors to.
            */
        public fun importData(urlOrFileName: String?, context: Context, callback: (String?) -> Unit, error: ((String?) -> Unit)?) {
            if (urlOrFileName != null && callback != null) {
                val executor: ExecutorService = Executors.newSingleThreadExecutor()
                val handler = Handler(Looper.getMainLooper())
                executor.execute {
                    var data: String? = null
                    
                    try {
                        data = if (URLUtil.isNetworkUrl(urlOrFileName)) {
                            URL(urlOrFileName).readText()
                        } else { //Assume file is in assets folder.
                            context.assets.open(urlOrFileName).bufferedReader().use{
                                it.readText()
                            }
                        }

                        handler.post {
                            //Ensure the resulting data string is not null or empty.
                            if (data != null && !data.isEmpty()) {
                                callback(data)
                            } else {
                                error!!("No data imported.")
                            }
                        }
                    } catch (e: Exception) {
                        error!!(e.message)
                    }
                }
            }
        }
    }
}
```

::: zone-end

O código abaixo mostra como usar este utilitário para importar dados da GeoJSON como uma cadeia e devolvê-lo à linha UI através de uma chamada. No retorno, os dados de cadeia podem ser serializados numa recolha de Recurso GeoJSON e adicionados à fonte de dados. Opcionalmente, atualize a câmara de mapas para se concentrar nos dados.

::: zone pivot="programming-language-java-android"

```java
//Create a data source and add it to the map.
DataSource dataSource = new DataSource();
map.sources.add(dataSource);

//Import the geojson data and add it to the data source.
Utils.importData("URL_or_FilePath_to_GeoJSON_data",
    this,
    (String result) -> {
        //Parse the data as a GeoJSON Feature Collection.
        FeatureCollection fc = FeatureCollection.fromJson(result);

        //Add the feature collection to the data source.
        dataSource.add(fc);

        //Optionally, update the maps camera to focus in on the data.

        //Calculate the bounding box of all the data in the Feature Collection.
        BoundingBox bbox = MapMath.fromData(fc);

        //Update the maps camera so it is focused on the data.
        map.setCamera(
            bounds(bbox),
            padding(20));
    });
```

::: zone-end

::: zone pivot="programming-language-kotlin"

```kotlin
//Create a data source and add it to the map.
DataSource source = new DataSource();
map.sources.add(source);

//Import the GeoJSON data and add it to the data source.
Utils.importData("SamplePoiDataSet.json", this) { 
    result: String? ->
        //Parse the data as a GeoJSON Feature Collection.
            val fc = FeatureCollection.fromJson(result!!)

        //Add the feature collection to the data source.
        source.add(fc)

        //Optionally, update the maps camera to focus in on the data.

        //Calculate the bounding box of all the data in the Feature Collection.
        val bbox = MapMath.fromData(fc);

        //Update the maps camera so it is focused on the data.
        map.setCamera(
            bounds(bbox),

            //Padding added to account for pixel size of rendered points.
            padding(20)
        )
    }
```

::: zone-end

## <a name="vector-tile-source"></a>Fonte de azulejos do vetor

Uma fonte de azulejos do vetor descreve como aceder a uma camada de azulejo vetorial. Use a `VectorTileSource` classe para instantaneaizar uma fonte de azulejos vetoriais. As camadas de azulejos vetoriais são semelhantes às camadas de azulejos, mas não são as mesmas. Uma camada de azulejo é uma imagem raster. As camadas de azulejos vetoriais são um ficheiro comprimido, em formato **PBF.** Este ficheiro comprimido contém dados de mapas de vetores e uma ou mais camadas. O ficheiro pode ser renderizado e modelado no cliente, com base no estilo de cada camada. Os dados de um azulejo vetorial contêm características geográficas sob a forma de pontos, linhas e polígonos. Existem várias vantagens de usar camadas de azulejos vetoriais em vez de camadas de azulejos rasteres:

- Um tamanho de arquivo de um azulejo vetorial é tipicamente muito menor do que um azulejo raster equivalente. Como tal, é utilizada menos largura de banda. Significa menor latência, um mapa mais rápido, e uma melhor experiência de utilizador.
- Uma vez que os azulejos vetoriais são renderizados no cliente, eles adaptam-se à resolução do dispositivo em que estão a ser exibidos. Como resultado, os mapas renderizados parecem mais bem definidos, com rótulos cristalinos.
- Alterar o estilo dos dados nos mapas de vetores não requer o download dos dados novamente, uma vez que o novo estilo pode ser aplicado no cliente. Em contraste, mudar o estilo de uma camada de azulejo raster normalmente requer carregar azulejos do servidor e, em seguida, aplicar o novo estilo.
- Uma vez que os dados são entregues em forma de vetor, há menos processamento do lado do servidor necessário para preparar os dados. Como resultado, os dados mais recentes podem ser disponibilizados mais rapidamente.

Azure Maps adere à [especificação do azulejo do vetor mapbox,](https://github.com/mapbox/vector-tile-spec)um padrão aberto. A Azure Maps fornece os seguintes serviços de azulejos vetoriais como parte da plataforma:

- Detalhes do formato de dados [de documentação](/rest/api/maps/renderv2/getmaptilepreview)de azulejos  |  [de estrada](https://developer.tomtom.com/maps-api/maps-api-documentation-vector/tile)
- Incidentes de [tráfego documentação](/rest/api/maps/traffic/gettrafficincidenttile)  |  [dados detalhes do formato de dados](https://developer.tomtom.com/traffic-api/traffic-api-documentation-traffic-incidents/vector-incident-tiles)
- Detalhes do formato de [dados de documentação](/rest/api/maps/traffic/gettrafficflowtile)de fluxo de tráfego  |  [](https://developer.tomtom.com/traffic-api/traffic-api-documentation-traffic-flow/vector-flow-tiles)
- O Azure Maps Creator também permite que os azulejos vetores personalizados sejam criados e acedidos através do [Get Tile Render V2](/rest/api/maps/renderv2/getmaptilepreview)

> [!TIP]
> Ao utilizar azulejos de imagem vetor ou raster do Azure Maps, o serviço de prestação de dados com a web SDK, pode `atlas.microsoft.com` substituir-se pelo espaço reservado `azmapsdomain.invalid` . Este espaço reservado será substituído pelo mesmo domínio utilizado pelo mapa e anexará automaticamente os mesmos detalhes de autenticação também. Isto simplifica consideravelmente a autenticação com o serviço de prestação quando se utiliza a autenticação do Azure Ative Directory.

Para exibir dados de uma fonte de azulejos vetoriais no mapa, ligue a fonte a uma das camadas de renderização de dados. Todas as camadas que usam uma fonte vetorial devem especificar um `sourceLayer` valor nas opções. O código seguinte carrega o serviço de vetor de fluxo de tráfego Azure Maps como uma fonte de azulejo vetorial, em seguida, exibe-o em um mapa usando uma camada de linha. Esta fonte de azulejos vetoriais tem um único conjunto de dados na camada de origem chamada "Fluxo de tráfego". Os dados da linha neste conjunto de dados têm uma propriedade chamada `traffic_level` que é usada neste código para selecionar a cor e escalar o tamanho das linhas.

::: zone pivot="programming-language-java-android"

```java
//Formatted URL to the traffic flow vector tiles, with the maps subscription key appended to it.
String trafficFlowUrl = "https://azmapsdomain.invalid/traffic/flow/tile/pbf?api-version=1.0&style=relative&zoom={z}&x={x}&y={y}";

//Create a vector tile source and add it to the map.
VectorTileSource source = new VectorTileSource(
    tiles(new String[] { trafficFlowUrl }),
    maxSourceZoom(22)
);
map.sources.add(source);

//Create a layer for traffic flow lines.
LineLayer layer = new LineLayer(source,
    //The name of the data layer within the data source to pass into this rendering layer.
    sourceLayer("Traffic flow"),

    //Color the roads based on the traffic_level property.
    strokeColor(
        interpolate(
            linear(),
            get("traffic_level"),
            stop(0, color(Color.RED)),
            stop(0.33, color(Color.YELLOW)),
            stop(0.66, color(Color.GREEN))
        )
    ),

    //Scale the width of roads based on the traffic_level property.
    strokeWidth(
        interpolate(
            linear(),
            get("traffic_level"),
            stop(0, 6),
            stop(1,1)
        )
    )
);

//Add the traffic flow layer below the labels to make the map clearer.
map.layers.add(layer, "labels");
```

::: zone-end

::: zone pivot="programming-language-kotlin"

```kotlin
//Formatted URL to the traffic flow vector tiles, with the maps subscription key appended to it.
val trafficFlowUrl = "https://azmapsdomain.invalid/traffic/flow/tile/pbf?api-version=1.0&style=relative&zoom={z}&x={x}&y={y}"

//Create a vector tile source and add it to the map.
val source = VectorTileSource(
    tiles(arrayOf(trafficFlowUrl)),
    maxSourceZoom(22)
)
map.sources.add(source)

//Create a layer for traffic flow lines.
val layer = LineLayer(
    source,  //The name of the data layer within the data source to pass into this rendering layer.
    sourceLayer("Traffic flow"),  //Color the roads based on the traffic_level property.
    strokeColor(
        interpolate(
            linear(),
            get("traffic_level"),
            stop(0, color(Color.RED)),
            stop(0.33, color(Color.YELLOW)),
            stop(0.66, color(Color.GREEN))
        )
    ),  //Scale the width of roads based on the traffic_level property.
    strokeWidth(
        interpolate(
            linear(),
            get("traffic_level"),
            stop(0, 6),
            stop(1, 1)
        )
    )
)

//Add the traffic flow layer below the labels to make the map clearer.
map.layers.add(layer, "labels")
```

::: zone-end

![Mapa com linhas de estrada codificadas por cores mostrando os níveis de fluxo de tráfego](media/create-data-source-android-sdk/android-vector-tile-source-line-layer.png)

## <a name="connecting-a-data-source-to-a-layer"></a>Ligação de uma fonte de dados a uma camada

Os dados são renderizados no mapa utilizando camadas de renderização. Uma única fonte de dados pode ser referenciada por uma ou mais camadas de renderização. As seguintes camadas de renderização requerem uma fonte de dados:

- [Camada de](map-add-bubble-layer-android.md) bolha - torna os dados de ponto como círculos escalonado no mapa.
- [Symbol layer](how-to-add-symbol-to-android-map.md) - torna os dados de ponto como ícones ou texto.
- [Heat map layer](map-add-heat-map-layer-android.md) - torna os dados do ponto como um mapa de calor de densidade.
- [Camada de linha](android-map-add-line-layer.md) - torne uma linha e ou torne o contorno dos polígonos.
- [Polygon layer](how-to-add-shapes-to-android-map.md) - preenche a área de um polígono com uma cor sólida ou padrão de imagem.

O código que se segue mostra como criar uma fonte de dados, adicioná-la ao mapa e conectá-la a uma camada de bolha. E então, importar dados de pontos GeoJSON de uma localização remota para a fonte de dados.

::: zone pivot="programming-language-java-android"

```java
//Create a data source and add it to the map.
DataSource source = new DataSource();
map.sources.add(source);

//Create a layer that defines how to render points in the data source and add it to the map.
BubbleLayer layer = new BubbleLayer(source);
map.layers.add(layer);

//Import the geojson data and add it to the data source.
Utils.importData("URL_or_FilePath_to_GeoJSON_data",
    this,
    (String result) -> {
        //Parse the data as a GeoJSON Feature Collection.
        FeatureCollection fc = FeatureCollection.fromJson(result);

        //Add the feature collection to the data source.
        dataSource.add(fc);

        //Optionally, update the maps camera to focus in on the data.

        //Calculate the bounding box of all the data in the Feature Collection.
        BoundingBox bbox = MapMath.fromData(fc);

        //Update the maps camera so it is focused on the data.
        map.setCamera(
            bounds(bbox),
            padding(20));
    });
```

::: zone-end

::: zone pivot="programming-language-kotlin"

```kotlin
//Create a data source and add it to the map.
val source = DataSource()
map.sources.add(source)

//Create a layer that defines how to render points in the data source and add it to the map.
val layer = BubbleLayer(source)
map.layers.add(layer)

//Import the geojson data and add it to the data source.
Utils.importData("URL_or_FilePath_to_GeoJSON_data", this) { 
    result: String? ->
        //Parse the data as a GeoJSON Feature Collection.
        val fc = FeatureCollection.fromJson(result!!)
    
        //Add the feature collection to the data source.
        dataSource.add(fc)
    
        //Optionally, update the maps camera to focus in on the data.
        //Calculate the bounding box of all the data in the Feature Collection.
        val bbox = MapMath.fromData(fc)
    
        //Update the maps camera so it is focused on the data.
        map.setCamera(
            bounds(bbox),
            padding(20)
        )
    }
```

::: zone-end

Existem camadas de renderização adicionais que não se ligam a estas fontes de dados, mas carregam diretamente os dados para renderização.

- [Camada de azulejo](how-to-add-tile-layer-android-map.md) - sobrepõe-se a uma camada de azulejo raster no topo do mapa.

## <a name="one-data-source-with-multiple-layers"></a>Uma fonte de dados com várias camadas

Várias camadas podem ser ligadas a uma única fonte de dados. Há muitos cenários diferentes em que esta opção é útil. Por exemplo, considere o cenário em que um utilizador desenha um polígono. Devemos renderizar e preencher a área do polígono à medida que o utilizador adiciona pontos ao mapa. Adicionar uma linha de estilo para delinear o polígono facilita a raridade de ver as bordas do polígono, à medida que o utilizador desenha. Para editar convenientemente uma posição individual no polígono, podemos adicionar uma pega, como um pino ou um marcador, acima de cada posição.

![Mapa mostrando várias camadas renderizando dados de uma única fonte de dados](media/create-data-source-android-sdk/multiple-layers-one-datasource.png)

Na maioria das plataformas de mapeamento, você precisaria de um objeto de polígono, um objeto de linha, e um pino para cada posição no polígono. À medida que o polígono é modificado, você precisaria atualizar manualmente a linha e os pinos, que podem rapidamente tornar-se complexos.

Com o Azure Maps, tudo o que precisa é de um único polígono numa fonte de dados, como mostra o código abaixo.

::: zone pivot="programming-language-java-android"

```java
//Create a data source and add it to the map.
DataSource source = new DataSource();
map.sources.add(source);

//Create a polygon and add it to the data source.
source.add(Polygon.fromLngLats(/* List of points */));

//Create a polygon layer to render the filled in area of the polygon.
PolygonLayer polygonLayer = new PolygonLayer(source,
    fillColor("rgba(255,165,0,0.2)")
);

//Create a line layer for greater control of rendering the outline of the polygon.
LineLayer lineLayer = new LineLayer(source,
    strokeColor("orange"),
    strokeWidth(2f)
);

//Create a bubble layer to render the vertices of the polygon as scaled circles.
BubbleLayer bubbleLayer = new BubbleLayer(source,
    bubbleColor("orange"),
    bubbleRadius(5f),
    bubbleStrokeColor("white"),
    bubbleStrokeWidth(2f)
);

//Add all layers to the map.
map.layers.add(new Layer[] { polygonLayer, lineLayer, bubbleLayer });
```

::: zone-end

::: zone pivot="programming-language-kotlin"

```kotlin
//Create a data source and add it to the map.
val source = DataSource()
map.sources.add(source)

//Create a polygon and add it to the data source.
source.add(Polygon.fromLngLats())

//Create a polygon layer to render the filled in area of the polygon.
val polygonLayer = PolygonLayer(
    source,
    fillColor("rgba(255,165,0,0.2)")
)

//Create a line layer for greater control of rendering the outline of the polygon.
val lineLayer = LineLayer(
    source,
    strokeColor("orange"),
    strokeWidth(2f)
)

//Create a bubble layer to render the vertices of the polygon as scaled circles.
val bubbleLayer = BubbleLayer(
    source,
    bubbleColor("orange"),
    bubbleRadius(5f),
    bubbleStrokeColor("white"),
    bubbleStrokeWidth(2f)
)

//Add all layers to the map.
map.layers.add(arrayOf<Layer>(polygonLayer, lineLayer, bubbleLayer))
```

::: zone-end

> [!TIP]
> Ao adicionar camadas ao mapa utilizando o `map.layers.add` método, o ID ou a instância de uma camada existente podem ser passados como um segundo parâmetro. Isto diria que o mapa para inserir a nova camada a ser adicionada abaixo da camada existente. Além de passar em uma camada ID este método também suporta os seguintes valores.
>
> - `"labels"` - Insere a nova camada abaixo das camadas de etiqueta do mapa.
> - `"transit"` - Insere a nova camada abaixo das camadas de estrada do mapa e do trânsito.

## <a name="next-steps"></a>Passos seguintes

Consulte os seguintes artigos para obter mais amostras de código para adicionar aos seus mapas:

> [!div class="nextstepaction"]
> [Utilizar expressões de estilo com base em dados](create-data-source-android-sdk.md)

> [!div class="nextstepaction"]
> [Adicionar uma camada de símbolo](how-to-add-symbol-to-android-map.md)

> [!div class="nextstepaction"]
> [Adicionar uma camada de bolha](map-add-bubble-layer-android.md)

> [!div class="nextstepaction"]
> [Adicionar uma camada de linhas](android-map-add-line-layer.md)

> [!div class="nextstepaction"]
> [Adicionar uma camada de polígonos](how-to-add-shapes-to-android-map.md)

> [!div class="nextstepaction"]
> [Adicione um mapa de calor](map-add-heat-map-layer-android.md)

> [!div class="nextstepaction"]
> [Amostras do Código SDK da Web](/samples/browse/?products=azure-maps)