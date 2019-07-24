---
title: 'Início rápido: Chamar seu ponto de extremidade de Pesquisa Personalizada do Bing usando Java | Microsoft Docs'
titleSuffix: Azure Cognitive Services
description: Use este guia de início rápido para começar a solicitar resultados de pesquisa de sua instância de Pesquisa Personalizada do Bing em Java.
services: cognitive-services
author: aahill
manager: nitinme
ms.service: cognitive-services
ms.subservice: bing-custom-search
ms.topic: quickstart
ms.date: 03/04/2019
ms.author: aahi
ms.openlocfilehash: 9af82ae8cff2e93c0456ed8b5a84414f98ba5b06
ms.sourcegitcommit: 9dc7517db9c5817a3acd52d789547f2e3efff848
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 07/23/2019
ms.locfileid: "68405277"
---
# <a name="quickstart-call-your-bing-custom-search-endpoint-using-java"></a>Início rápido: Chamar seu ponto de extremidade de Pesquisa Personalizada do Bing usando Java

Use este guia de início rápido para começar a solicitar resultados de pesquisa de sua instância de Pesquisa Personalizada do Bing. Embora esse aplicativo seja escrito em Java, o API de Pesquisa Personalizada do Bing é um serviço Web RESTful compatível com a maioria das linguagens de programação. O código fonte deste exemplo está disponível no [GitHub](https://github.com/Azure-Samples/cognitive-services-REST-api-samples/blob/master/java/Search/BingCustomSearchv7.java).

## <a name="prerequisites"></a>Pré-requisitos

- Uma instância de Pesquisa Personalizada do Bing. Consulte [início rápido: Crie sua primeira instância](quick-start.md) de pesquisa personalizada do Bing para obter mais informações.

- O [Kit de desenvolvimento Java](https://www.oracle.com/technetwork/java/javase/downloads/index.html) mais recente  

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

2. Crie uma classe chamada `CustomSrchJava`e crie variáveis para sua chave de assinatura, ponto de extremidade de pesquisa personalizado e a ID de configuração personalizada da sua instância de pesquisa. 
    ```java
    public class CustomSrchJava {
        static String host = "https://api.cognitive.microsoft.com";
        static String path = "/bingcustomsearch/v7.0/search";
        static String subscriptionKey = "YOUR-SUBSCRIPTION-KEY"; 
        static String customConfigId = "YOUR-CUSTOM-CONFIG-ID";
        static String searchTerm = "Microsoft";  
    ...
    ```

3. Crie outra classe chamada `SearchResults` para conter a resposta de sua instância de pesquisa personalizada do Bing.

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

4. Crie uma função chamada `prettify()` para formatar a resposta JSON do API de pesquisa personalizada do Bing.

    ```java
        // pretty-printer for JSON; uses GSON parser to parse and re-serialize
        public static String prettify(String json_text) {
            JsonParser parser = new JsonParser();
            JsonObject json = parser.parse(json_text).getAsJsonObject();
            Gson gson = new GsonBuilder().setPrettyPrinting().create();
            return gson.toJson(json);
        }
    ```

## <a name="send-and-receive-a-search-request"></a>Enviar e receber uma solicitação de pesquisa 

1. Crie uma função chamada `SearchWeb()` que envia uma solicitação e retorna um `SearchResults` objeto. Crie a URL de solicitação combinando suas informações de ID de configuração, consulta e ponto de extremidade personalizadas. Adicione sua chave de assinatura ao `Ocp-Apim-Subscription-Key` cabeçalho.

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

2. Crie um fluxo e armazene a resposta JSON em `SearchResults` um objeto.

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

3. No método principal do seu aplicativo, chame `SearchWeb()` com seu termo de pesquisa, 

    ```java
    System.out.println("\nJSON Response:\n");
    System.out.println(prettify(result.jsonResponse));
    ```

4. Execute o programa.
    
## <a name="next-steps"></a>Passos Seguintes

> [!div class="nextstepaction"]
> [Criar um aplicativo Web de pesquisa personalizado](./tutorials/custom-search-web-page.md)
