---
title: 'Quickstart: Ligue para o seu ponto final de pesquisa personalizada bing usando Java / Microsoft Docs'
titleSuffix: Azure Cognitive Services
description: Use este quickstart para começar a solicitar resultados de pesquisa da sua instância de Pesquisa Personalizada Bing em Java.
services: cognitive-services
author: aahill
manager: nitinme
ms.service: cognitive-services
ms.subservice: bing-custom-search
ms.topic: quickstart
ms.date: 05/08/2020
ms.author: aahi
ms.openlocfilehash: 292b94e56d988a1126db83e2f8ce40bbb0af6f42
ms.sourcegitcommit: 64fc70f6c145e14d605db0c2a0f407b72401f5eb
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 05/27/2020
ms.locfileid: "83873249"
---
# <a name="quickstart-call-your-bing-custom-search-endpoint-using-java"></a>Quickstart: Ligue para o seu ponto final de pesquisa personalizada bing usando Java

Utilize este quickstart para aprender a solicitar resultados de pesquisa a partir da sua instância de Pesquisa Personalizada Bing. Embora esta aplicação esteja escrita em Java, o Bing Custom Search API é um serviço web RESTful compatível com a maioria dos idiomas de programação. O código fonte desta amostra está disponível no [GitHub](https://github.com/Azure-Samples/cognitive-services-REST-api-samples/blob/master/java/Search/BingCustomSearchv7.java).

## <a name="prerequisites"></a>Pré-requisitos

- Uma instância de pesquisa personalizada bing. Para mais informações, consulte [Quickstart: Crie a sua primeira instância](quick-start.md)de pesquisa personalizada bing .

- O mais recente Kit de [Desenvolvimento java.](https://www.oracle.com/technetwork/java/javase/downloads/index.html)

- A [biblioteca Gson.](https://github.com/google/gson)

[!INCLUDE [cognitive-services-bing-custom-search-prerequisites](../../../includes/cognitive-services-bing-custom-search-signup-requirements.md)]

## <a name="create-and-initialize-the-application"></a>Criar e inicializar a aplicação

1. Crie um novo projeto Java no seu IDE ou editor favorito, e importe as seguintes bibliotecas:

    ```java
    import java.io.InputStream;
    import java.net.URL;
    import java.net.URLEncoder;
    import java.util.HashMap;
    import java.util.List;
    import java.util.Map;
    import java.util.Scanner;
    import javax.net.ssl.HttpsURLConnection;
    import com.google.gson.Gson;
    import com.google.gson.GsonBuilder;
    import com.google.gson.JsonObject;
    import com.google.gson.JsonParser;
    ```

2. Crie uma classe chamada `CustomSrchJava` , e, em seguida, crie variáveis para a sua chave de subscrição, ponto final de pesquisa personalizado e ID de configuração personalizada da instância de pesquisa. Pode utilizar o ponto final global no seguinte código ou utilizar o ponto final de [subdomínio personalizado](../../cognitive-services/cognitive-services-custom-subdomains.md) exibido no portal Azure para o seu recurso.
    ```java
    public class CustomSrchJava {
        static String host = "https://api.cognitive.microsoft.com";
        static String path = "/bingcustomsearch/v7.0/search";
        static String subscriptionKey = "YOUR-SUBSCRIPTION-KEY"; 
        static String customConfigId = "YOUR-CUSTOM-CONFIG-ID";
        static String searchTerm = "Microsoft";  
    ...
    ```

3. Crie outra classe com o nome `SearchResults` para conter a resposta da sua instância de Pesquisa Personalizada Bing.

    ```java
    class SearchResults {
        HashMap<String, String> relevantHeaders;
        String jsonResponse;
        SearchResults(HashMap<String, String> headers, String json) {
            relevantHeaders = headers;
            jsonResponse = json;
        }
    }
    ```

4. Crie uma função nomeada `prettify()` para formatar a resposta JSON a partir da API de Pesquisa Personalizada bing.

    ```java
        // pretty-printer for JSON; uses GSON parser to parse and re-serialize
        public static String prettify(String json_text) {
            JsonParser parser = new JsonParser();
            JsonObject json = parser.parse(json_text).getAsJsonObject();
            Gson gson = new GsonBuilder().setPrettyPrinting().create();
            return gson.toJson(json);
        }
    ```

## <a name="send-and-receive-a-search-request"></a>Enviar e receber um pedido de pesquisa 

1. Crie uma função nomeada `SearchWeb()` que envie um pedido e deredere um `SearchResults` objeto. Crie o url de pedido combinando o seu ID de configuração personalizado, consulta e informações de ponto final. Adicione a sua chave de subscrição ao `Ocp-Apim-Subscription-Key` cabeçalho.

    ```java
    public class CustomSrchJava {
    ...
        public static SearchResults SearchWeb (String searchQuery) throws Exception {
            // construct the URL for your search request (endpoint + query string)
            URL url = new URL(host + path + "?q=" +  URLEncoder.encode(searchTerm, "UTF-8") + "&CustomConfig=" + customConfigId);
            HttpsURLConnection connection = (HttpsURLConnection)url.openConnection();
            connection.setRequestProperty("Ocp-Apim-Subscription-Key", subscriptionKey);
    ...
    ```

2. Crie um fluxo e guarde a resposta JSON num `SearchResults` objeto.

    ```java
    public class CustomSrchJava {
    ...
        public static SearchResults SearchWeb (String searchQuery) throws Exception {
            ...
            // receive the JSON body
            InputStream stream = connection.getInputStream();
            String response = new Scanner(stream).useDelimiter("\\A").next();
        
            // construct result object for return
            SearchResults results = new SearchResults(new HashMap<String, String>(), response);
            
            stream.close();
            return results;
        }
    ```

3. Imprima a resposta jSON.

    ```java
    System.out.println("\nJSON Response:\n");
    System.out.println(prettify(result.jsonResponse));
    ```

4. Execute o programa.
    
## <a name="next-steps"></a>Passos seguintes

> [!div class="nextstepaction"]
> [Construa uma aplicação web de pesquisa personalizada](./tutorials/custom-search-web-page.md)
