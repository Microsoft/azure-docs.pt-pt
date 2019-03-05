---
title: 'Início rápido: Chamar o ponto final de pesquisa personalizada do Bing com Java | Documentos da Microsoft'
titlesuffix: Azure Cognitive Services
description: Utilize este guia de introdução para começar a pedir os resultados da pesquisa da sua instância de pesquisa personalizada do Bing em Java.
services: cognitive-services
author: aahill
manager: nitinme
ms.service: cognitive-services
ms.subservice: bing-custom-search
ms.topic: quickstart
ms.date: 03/04/2019
ms.author: aahi
ms.openlocfilehash: fc1c7d2730d68fb8e3b51a39e0cb89b4098418d6
ms.sourcegitcommit: 8b41b86841456deea26b0941e8ae3fcdb2d5c1e1
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/05/2019
ms.locfileid: "57337027"
---
# <a name="quickstart-call-your-bing-custom-search-endpoint-using-java"></a>Início rápido: Chamar o ponto final de pesquisa personalizada do Bing através de Java

Utilize este guia de introdução para começar a pedir os resultados da pesquisa da sua instância de pesquisa personalizada do Bing. Embora esse aplicativo é escrito em Java, a API de pesquisa personalizada do Bing é um serviço RESTful web compatível com a maioria das linguagens de programação. O código fonte deste exemplo está disponível no [GitHub](https://github.com/Azure-Samples/cognitive-services-REST-api-samples/blob/master/java/Search/BingCustomSearchv7.java).

## <a name="prerequisites"></a>Pré-requisitos

- Uma instância de pesquisa personalizada do Bing. Consulte [início rápido: Criar a primeira instância de pesquisa personalizada do Bing](quick-start.md) para obter mais informações.

- A versão mais recente [Java Development Kit](https://www.oracle.com/technetwork/java/javase/downloads/index.html)  

- A [Biblioteca de Gson](https://github.com/google/gson)

[!INCLUDE [cognitive-services-bing-custom-search-prerequisites](../../../includes/cognitive-services-bing-custom-search-signup-requirements.md)]

## <a name="create-and-initialize-the-application"></a>Criar e inicializar a aplicação

1. Crie um novo projeto Java no seu IDE ou editor favorito e importe as seguintes bibliotecas.

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

2. criar uma classe chamada `CustomSrchJava`e criar variáveis para a chave de subscrição, ponto final de pesquisa personalizada e personalizada configuração ID. da sua instância de pesquisa 
    ```java
    public class CustomSrchJava {
        static String host = "https://api.cognitive.microsoft.com";
        static String path = "/bingcustomsearch/v7.0/search";
        static String subscriptionKey = "YOUR-SUBSCRIPTION-KEY"; 
        static String customConfigId = "YOUR-CUSTOM-CONFIG-ID";
        static String searchTerm = "Microsoft";  
    ...
    ```

3. Criar outra classe com o nome `SearchResults` para conter a resposta da sua instância de pesquisa personalizada do Bing.

    ```csharp
    class SearchResults{
        HashMap<String, String> relevantHeaders;
        String jsonResponse;
        SearchResults(HashMap<String, String> headers, String json) {
            relevantHeaders = headers;
            jsonResponse = json;
        }
    }
    ```

4. Criar uma função chamada `prettify()` para formatar a resposta JSON da API de pesquisa personalizada do Bing.

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

1. Criar uma função chamada `SearchWeb()` que envia um pedido e retorna um `SearchResults` objeto. Criar o pedido de url ao combinar as suas informações de ID de configuração personalizada, a consulta e o ponto final. Adicionar a chave de subscrição para o `Ocp-Apim-Subscription-Key` cabeçalho.

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

2. Criar um fluxo e armazenar a resposta JSON num `SearchResults` objeto.

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

3. O método principal de seu aplicativo, chame `SearchWeb()` com o termo de pesquisa, 

    ```java
    System.out.println("\nJSON Response:\n");
    System.out.println(prettify(result.jsonResponse));
    ```

4. Execute o programa.
    
## <a name="next-steps"></a>Passos Seguintes

> [!div class="nextstepaction"]
> [Criar uma aplicação web de pesquisa personalizada](./tutorials/custom-search-web-page.md)
