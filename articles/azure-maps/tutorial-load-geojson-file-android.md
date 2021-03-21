---
title: 'Tutorial: Carregue os dados da GeoJSON no Azure Maps Android SDK | Microsoft Azure Maps'
description: Tutorial sobre como carregar o ficheiro de dados da GeoJSON no mapa Android do Azure Maps SDK.
author: rbrundritt
ms.author: richbrun
ms.date: 12/10/2020
ms.topic: tutorial
ms.service: azure-maps
services: azure-maps
manager: cpendle
zone_pivot_groups: azure-maps-android
ms.openlocfilehash: 8300a7c120ce816c8068a88fa69f4f978fa664ca
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/20/2021
ms.locfileid: "102034511"
---
# <a name="tutorial-load-geojson-data-into-azure-maps-android-sdk"></a>Tutorial: Carregue os dados da GeoJSON no Azure Maps Android SDK

Este tutorial guia-o através do processo de importação de um ficheiro GeoJSON de dados de localização para o Azure Maps Android SDK. Neste tutorial, ficará a saber como:

> [!div class="checklist"]
> * Adicione Azure Maps a uma aplicação Android.
> * Crie uma fonte de dados e carregue num ficheiro GeoJSON a partir de um ficheiro local ou da web.
> * Exiba os dados no mapa.

## <a name="prerequisites"></a>Pré-requisitos

1. Complete o [Quickstart: Crie uma aplicação Android.](quick-android-map.md) Este tutorial irá estender o código usado nesse arranque rápido.
2. Descarregue o ficheiro [Pontos de Interesse da Amostra](https://raw.githubusercontent.com/Azure-Samples/AzureMapsCodeSamples/master/AzureMapsCodeSamples/Common/data/geojson/SamplePoiDataSet.json) GeoJSON.

### <a name="import-geojson-data-from-web-or-assets-folder"></a>Importar dados da GeoJSON a partir de pasta web ou ativos

A maioria dos ficheiros GeoJSON embrulham todos os dados dentro de um `FeatureCollection` . Com isto em mente, se os ficheiros GeoJSON forem carregados na aplicação como uma corda, podem ser passados para o método estático da coleção `fromJson` de funcionalidades, que irá deserializar a cadeia num objeto GeoJSON `FeatureCollection` que pode ser adicionado ao mapa.

Os passos seguintes mostram como importar um ficheiro GeoJSON para a aplicação e deseralizá-lo como `FeatureCollection` um objeto GeoJSON.

1. Complete o [Quickstart: Crie uma aplicação Android](quick-android-map.md) à medida que os seguintes passos se constroem em cima desta aplicação.
2. No painel de projeto do estúdio Android, clique com o botão direito na pasta da **aplicação** e vá para `New > Folder > Assets Folder` .
3. Arraste e deixe cair o ficheiro [Pontos de Interesse de Amostra](https://raw.githubusercontent.com/Azure-Samples/AzureMapsCodeSamples/master/AzureMapsCodeSamples/Common/data/geojson/SamplePoiDataSet.json) GeoJSON na pasta de ativos.

::: zone pivot="programming-language-java-android"

4. Crie um novo ficheiro chamado **Utils.java** e adicione o seguinte código a esse ficheiro. Este código fornece um método estático chamado `importData` que assíncrona importa um ficheiro da `assets` pasta da aplicação ou da web usando um URL como uma cadeia e devolve-o de volta ao fio de UI usando um método de retorno simples.

    ```java
    //Modify the package name as needed to align with your application.
    package com.example.myapplication;
    
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

5. Entre no ficheiro **MainActivity.java** e adicione o seguinte código dentro da chamada para o `mapControl.onReady` evento, este está localizado dentro do `onCreate` método. Este código usa o utilitário de importação para ler no **SamplePoiDataSet.jsem** ficheiro como uma cadeia, em seguida, desseecia-o como uma coleção de recursos usando o método estático `fromJson` da `FeatureCollection` classe. Este código também calcula a área da caixa de limitação para todos os dados na recolha de funcionalidades e utiliza-o para definir a câmara do mapa para se concentrar nos dados.

    ```java
    //Create a data source and add it to the map.
    DataSource source = new DataSource();
    map.sources.add(source);
    
    //Import the GeoJSON data and add it to the data source.
    Utils.importData("SamplePoiDataSet.json",
        this,
        (String result) -> {
            //Parse the data as a GeoJSON Feature Collection.
            FeatureCollection fc = FeatureCollection.fromJson(result);
    
            //Add the feature collection to the data source.
            source.add(fc);
    
            //Optionally, update the maps camera to focus in on the data.
    
            //Calculate the bounding box of all the data in the Feature Collection.
            BoundingBox bbox = MapMath.fromData(fc);
    
            //Update the maps camera so it is focused on the data.
            map.setCamera(
                bounds(bbox),

                //Padding added to account for pixel size of rendered points.
                padding(20)
            );
        });
    ```

6. Utilizando o código para carregar os dados da GeoJSON uma fonte de dados, precisamos agora de especificar como esses dados devem ser apresentados no mapa. Existem várias camadas de renderização diferentes para dados de ponto; [Camada de bolha,](map-add-bubble-layer-android.md) [camada de símbolo](how-to-add-symbol-to-android-map.md)e camada de mapa de [calor](map-add-heat-map-layer-android.md) são as camadas mais usadas. Adicione o seguinte código para tornar os dados numa camada de bolha na chamada para o `mapControl.onReady` evento após o código de importação dos dados.

    ```java
    //Create a layer and add it to the map.
    BubbleLayer layer = new BubbleLayer(source);
    map.layers.add(layer);
    ```

::: zone-end

::: zone pivot="programming-language-kotlin"

4. Crie um novo ficheiro chamado **Utils.kt** e adicione o seguinte código a esse ficheiro. Este código fornece um método estático chamado `importData` que assíncrona importa um ficheiro da `assets` pasta da aplicação ou da web usando um URL como uma cadeia e devolve-o de volta ao fio de UI usando um método de retorno simples.

    ```kotlin
    //Modify the package name as needed to align with your application.
    package com.example.myapplication;

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

5. Entre no ficheiro **MainActivity.kt** e adicione o seguinte código dentro da chamada para o `mapControl.onReady` evento, este está localizado dentro do `onCreate` método. Este código usa o utilitário de importação para ler no **SamplePoiDataSet.jsem** ficheiro como uma cadeia, em seguida, desseecia-o como uma coleção de recursos usando o método estático `fromJson` da `FeatureCollection` classe. Este código também calcula a área da caixa de limitação para todos os dados na recolha de funcionalidades e utiliza-o para definir a câmara do mapa para se concentrar nos dados.

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

6. Utilizando o código para carregar os dados da GeoJSON uma fonte de dados, precisamos agora de especificar como esses dados devem ser apresentados no mapa. Existem várias camadas de renderização diferentes para dados de ponto; [Camada de bolha,](map-add-bubble-layer-android.md) [camada de símbolo](how-to-add-symbol-to-android-map.md)e camada de mapa de [calor](map-add-heat-map-layer-android.md) são as camadas mais usadas. Adicione o seguinte código para tornar os dados numa camada de bolha na chamada para o `mapControl.onReady` evento após o código de importação dos dados.

    ```kotlin
    //Create a layer and add it to the map.
    val layer = new BubbleLayer(source)
    map.layers.add(layer)
    ```

::: zone-end

7. Execute a aplicação. Será exibido um mapa focado nos EUA, com círculos sobrepostos para cada local no ficheiro GeoJSON.

    ![Mapa dos EUA com dados de um ficheiro GeoJSON exibido](media/tutorial-load-geojson-file-android/android-import-geojson.png)

## <a name="clean-up-resources"></a>Limpar os recursos

Tome as seguintes medidas para limpar os recursos deste tutorial:

1. Feche o Android Studio e elimine a aplicação que criou.
2. Se tiver testado a aplicação num dispositivo externo, desinstale a aplicação a partir desse dispositivo.

## <a name="next-steps"></a>Passos seguintes

Para ver mais exemplos de código e uma experiência interativa de programação:

> [!div class="nextstepaction"]
> [Utilizar expressões de estilo com base em dados](data-driven-style-expressions-android-sdk.md)

> [!div class="nextstepaction"]
> [Apresentar informações da funcionalidade](display-feature-information-android.md)

> [!div class="nextstepaction"]
> [Adicionar uma camada de símbolo](how-to-add-symbol-to-android-map.md)

> [!div class="nextstepaction"]
> [Adicionar uma camada de linhas](android-map-add-line-layer.md)

> [!div class="nextstepaction"]
> [Adicionar uma camada de polígonos](how-to-add-shapes-to-android-map.md)
