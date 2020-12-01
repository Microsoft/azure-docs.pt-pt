---
title: 'Quickstart: Procure vídeos usando a API REST e Java - Bing Video Search'
titleSuffix: Azure Cognitive Services
description: Utilize este quickstart para enviar pedidos de pesquisa de vídeo para a API Bing Video Search REST usando Java.
services: cognitive-services
author: aahill
manager: nitinme
ms.service: cognitive-services
ms.subservice: bing-video-search
ms.topic: quickstart
ms.date: 05/22/2020
ms.custom: devx-track-java
ms.author: aahi
ms.openlocfilehash: 6c34ec5582b8251d5b64bf24654b50d7c391cee4
ms.sourcegitcommit: 9eda79ea41c60d58a4ceab63d424d6866b38b82d
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 11/30/2020
ms.locfileid: "96341398"
---
# <a name="quickstart-search-for-videos-using-the-bing-video-search-rest-api-and-java"></a>Quickstart: Procure vídeos utilizando a API e Java de pesquisa de vídeo Bing

> [!WARNING]
> As APIs de Pesquisa de Bing estão a mover-se dos Serviços Cognitivos para os Serviços de Pesquisa Bing. A partir **de 30 de outubro de 2020,** quaisquer novos casos de Bing Search devem ser adquir-se na sequência do processo [aqui](/bing/search-apis/bing-web-search/create-bing-search-service-resource)documentado.
> Bing Search APIs aforados usando Serviços Cognitivos será suportado durante os próximos três anos ou até o final do seu Contrato de Empresa, o que acontecer primeiro.
> Para obter instruções de migração, consulte [os Serviços de Busca Bing.](/bing/search-apis/bing-web-search/create-bing-search-service-resource)

Use este quickstart para fazer a sua primeira chamada para a API de Pesquisa de Vídeo Bing. Esta simples aplicação Java envia uma consulta de pesquisa de vídeo HTTP para a API e exibe a resposta JSON. Embora esta aplicação esteja escrita em Java, a API é um serviço Web RESTful compatível com a maioria das linguagens de programação. 

O código-fonte desta amostra está disponível [no GitHub](https://github.com/Azure-Samples/cognitive-services-REST-api-samples/blob/master/java/Search/BingVideoSearchv7.java) com tratamento adicional de erros, funcionalidades e anotações de código.

## <a name="prerequisites"></a>Pré-requisitos

* O [Kit de Desenvolvimento java (JDK)](https://www.oracle.com/technetwork/java/javase/downloads/jdk11-downloads-5066655.html)

* A [Biblioteca de Gson](https://github.com/google/gson)

[!INCLUDE [cognitive-services-bing-video-search-signup-requirements](../../../../includes/cognitive-services-bing-video-search-signup-requirements.md)]


## <a name="create-and-initialize-a-project"></a>Criar e inicializar um projeto

1. Crie um novo projeto Java no seu IDE ou editor favorito, e importe as seguintes bibliotecas:

    ```java
    import java.net.*;
    import java.util.*;
    import java.io.*;
    import javax.net.ssl.HttpsURLConnection;
    import com.google.gson.Gson;
    import com.google.gson.GsonBuilder;
    import com.google.gson.JsonObject;
    import com.google.gson.JsonParser;
    ```

2. Crie uma nova classe nomeada `SearchResults` para armazenar os cabeçalhos e a resposta JSON da API.

    ```java
    // Container class for search results encapsulates relevant headers and JSON data
    class SearchResults{
        HashMap<String, String> relevantHeaders;
        String jsonResponse;
        SearchResults(HashMap<String, String> headers, String json) {
            relevantHeaders = headers;
            jsonResponse = json;
        }
    }
    ```

3. Crie um novo método nomeado `SearchVideos()` com variáveis para o seu anfitrião e caminho de ponto final da API, a sua chave de subscrição e o termo de pesquisa. Este método devolve um `SearchResults` objeto. Pelo `host` valor, pode utilizar o ponto final global no seguinte código ou utilizar o ponto final [de subdomínio personalizado](../../../cognitive-services/cognitive-services-custom-subdomains.md) apresentado no portal Azure para o seu recurso.

    ```java
    public static SearchResults SearchVideos (String searchQuery) throws Exception {
        static String subscriptionKey = "enter your key here";
        static String host = "https://api.cognitive.microsoft.com";
        static String path = "/bing/v7.0/videos/search";
        static String searchTerm = "kittens";
    }
    ```

## <a name="construct-and-send-the-search-request"></a>Construa e envie o pedido de pesquisa

No `SearchVideos()` método, execute os seguintes passos:

1. Construa o URL para o seu pedido combinando o seu anfitrião API, caminho e consulta de pesquisa codificada. Utilize `openConnection()` para criar uma ligação e, em seguida, adicione a chave de subscrição ao `Ocp-Apim-Subscription-Key` cabeçalho.

     ```java
     URL url = new URL(host + path + "?q=" +  URLEncoder.encode(searchQuery, "UTF-8"));
     HttpsURLConnection connection = (HttpsURLConnection)url.openConnection();
     connection.setRequestProperty("Ocp-Apim-Subscription-Key", subscriptionKey);
     ```

2. Obtenha a resposta da API e guarde a cadeia JSON.

     ```java
     InputStream stream = connection.getInputStream();
     String response = new Scanner(stream).useDelimiter("\\A").next();
     ```

 3. Utilize `getHeaderFields()` para extrair os cabeçalhos HTTP da resposta e guarde os relacionados com bing no `results` objeto. Em seguida, feche o fluxo e devolva o resultado.

     ```java
     // extract Bing-related HTTP headers
     Map<String, List<String>> headers = connection.getHeaderFields();
     for (String header : headers.keySet()) {
         if (header == null) continue;      // may have null key
         if (header.startsWith("BingAPIs-") || header.startsWith("X-MSEdge-")) {
             results.relevantHeaders.put(header, headers.get(header).get(0));
         }
     }
     stream.close();
     return results;
     ```

## <a name="format-the-response"></a>Formatar a resposta

Crie um método nomeado `prettify()` para formatar a resposta devolvida da API de Vídeo Bing. Use a biblioteca Gson `JsonParser` para converter uma corda JSON num objeto. Em seguida, use `GsonBuilder()` e crie a cadeia `toJson()` formatada.

```java
// pretty-printer for JSON; uses GSON parser to parse and re-serialize
public static String prettify(String json_text) {
    JsonParser parser = new JsonParser();
    JsonObject json = parser.parse(json_text).getAsJsonObject();
    Gson gson = new GsonBuilder().setPrettyPrinting().create();
    return gson.toJson(json);
}
```

## <a name="send-the-request-and-print-the-response"></a>Envie o pedido e imprima a resposta

No método principal da sua aplicação, ligue `SearchVideos` com o seu termo de pesquisa. Em seguida, imprima os cabeçalhos HTTP armazenados na resposta e a cadeia JSON devolvida pela API.

 ```java
 public static void main (String[] args) {

     SearchResults result = SearchVideos(searchTerm);
     //print the Relevant HTTP Headers
     for (String header : result.relevantHeaders.keySet())
         System.out.println(header + ": " + result.relevantHeaders.get(header));
     System.out.println(prettify(result.jsonResponse));
 }
 ```

## <a name="json-response"></a>Resposta JSON

É devolvida uma resposta com êxito em JSON, tal como é apresentado no exemplo seguinte:

```json
{
    "_type": "Videos",
    "instrumentation": {},
    "readLink": "https://api.cognitive.microsoft.com/api/v7/videos/search?q=kittens",
    "webSearchUrl": "https://www.bing.com/videos/search?q=kittens",
    "totalEstimatedMatches": 1000,
    "value": [
        {
            "webSearchUrl": "https://www.bing.com/videos/search?q=kittens&view=...",
            "name": "Top 10 cute kitten videos compilation",
            "description": "HELP HOMELESS ANIMALS AND WIN A PRIZE BY CHOOSING...",
            "thumbnailUrl": "https://tse4.mm.bing.net/th?id=OVP.n1aE_Oikl4MtzBb...",
            "datePublished": "2014-11-12T22:47:36.0000000",
            "publisher": [
                {
                    "name": "Fabrikam"
                }
            ],
            "creator": {
                "name": "Marcus Appel"
            },
            "isAccessibleForFree": true,
            "contentUrl": "https://www.fabrikam.com/watch?v=8HVWitAW-Qg",
            "hostPageUrl": "https://www.fabrikam.com/watch?v=8HVWitAW-Qg",
            "encodingFormat": "h264",
            "hostPageDisplayUrl": "https://www.fabrikam.com/watch?v=8HVWitAW-Qg",
            "width": 480,
            "height": 360,
            "duration": "PT3M52S",
            "motionThumbnailUrl": "https://tse4.mm.bing.net/th?id=OM.j4QyJAENJphdZQ_1501386166&pid=Api",
            "embedHtml": "<iframe width=\"1280\" height=\"720\" src=\"https://www.fabrikam.com/embed/8HVWitAW-Qg?autoplay=1\" frameborder=\"0\" allowfullscreen></iframe>",
            "allowHttpsEmbed": true,
            "viewCount": 7513633,
            "thumbnail": {
                "width": 300,
                "height": 168
            },
            "videoId": "655D98260D012432848F6558260D012432848F",
            "allowMobileEmbed": true,
            "isSuperfresh": false
        },
        . . .
    ],
    "nextOffset": 36,
    "queryExpansions": [
        {
            "text": "Kittens Meowing",
            "displayText": "Meowing",
            "webSearchUrl": "https://www.bing.com/videos/search?q=Kittens+Meowing...",
            "searchLink": "https://api.cognitive.microsoft.com/api/v7/videos/search...",
            "thumbnail": {
                "thumbnailUrl": "https://tse3.mm.bing.net/th?q=Kittens+Meowing&pid..."
            }
        },
        {
            "text": "Funny Kittens",
            "displayText": "Funny",
            "webSearchUrl": "https://www.bing.com/videos/search?q=Funny+Kittens...",
            "searchLink": "https://api.cognitive.microsoft.com/api/v7/videos/search...",
            "thumbnail": {
                "thumbnailUrl": "https://tse3.mm.bing.net/th?q=Funny+Kittens&..."
            }
        },
        . . .
    ],
    "pivotSuggestions": [
        {
            "pivot": "kittens",
            "suggestions": [
                {
                    "text": "Cat",
                    "displayText": "Cat",
                    "webSearchUrl": "https://www.bing.com/videos/search?q=Cat...",
                    "searchLink": "https://api.cognitive.microsoft.com/api/v7/videos/search?...",
                    "thumbnail": {
                        "thumbnailUrl": "https://tse3.mm.bing.net/th?q=Cat&pid=Api..."
                    }
                },
                {
                    "text": "Feral Cat",
                    "displayText": "Feral Cat",
                    "webSearchUrl": "https://www.bing.com/videos/search?q=Feral+Cat...",
                    "searchLink": "https://api.cognitive.microsoft.com/api/v7/videos/search...",
                    "thumbnail": {
                        "thumbnailUrl": "https://tse3.mm.bing.net/th?q=Feral+Cat&pid=Api&..."
                    }
                }
            ]
        }
    ],
    "relatedSearches": [
        {
            "text": "Kittens Being Born",
            "displayText": "Kittens Being Born",
            "webSearchUrl": "https://www.bing.com/videos/search?q=Kittens+Being+Born...",
            "searchLink": "https://api.cognitive.microsoft.com/api/v7/videos/search?...",
            "thumbnail": {
                "thumbnailUrl": "https://tse1.mm.bing.net/th?q=Kittens+Being+Born&pid=..."
            }
        },
        . . .
    ]
}
```

## <a name="next-steps"></a>Passos seguintes

> [!div class="nextstepaction"]
> [Criar uma aplicação web de página única](../tutorial-bing-video-search-single-page-app.md)

## <a name="see-also"></a>Ver também 

 [O que é a API de Pesquisa de Vídeos do Bing?](../overview.md)