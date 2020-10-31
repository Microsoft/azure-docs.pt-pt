---
title: 'Quickstart: Ligue para o seu ponto final de pesquisa personalizada Bing usando Java / Microsoft Docs'
titleSuffix: Azure Cognitive Services
description: Utilize este quickstart para começar a solicitar os resultados da pesquisa a partir do seu caso de Pesquisa Personalizada Bing em Java.
services: cognitive-services
author: aahill
manager: nitinme
ms.service: cognitive-services
ms.subservice: bing-custom-search
ms.topic: quickstart
ms.date: 05/08/2020
ms.custom: devx-track-java
ms.author: aahi
ms.openlocfilehash: 1dfc9c79e41a0dbeb08c9416316e601b05b2cb32
ms.sourcegitcommit: 3bdeb546890a740384a8ef383cf915e84bd7e91e
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/30/2020
ms.locfileid: "93080146"
---
# <a name="quickstart-call-your-bing-custom-search-endpoint-using-java"></a>Quickstart: Ligue para o seu ponto final de pesquisa personalizada Bing usando Java

> [!WARNING]
> As APIs de Pesquisa de Bing estão a mover-se dos Serviços Cognitivos para os Serviços de Pesquisa Bing. A partir **de 30 de outubro de 2020,** quaisquer novos casos de Bing Search devem ser adquir-se na sequência do processo [aqui](https://aka.ms/cogsvcs/bingmove)documentado.
> Bing Search APIs aforados usando Serviços Cognitivos será suportado durante os próximos três anos ou até o final do seu Contrato de Empresa, o que acontecer primeiro.
> Para obter instruções de migração, consulte [os Serviços de Busca Bing.](https://aka.ms/cogsvcs/bingmigration)

Utilize este quickstart para aprender a solicitar resultados de pesquisa a partir da sua instância de Pesquisa Personalizada Bing. Embora esta aplicação esteja escrita em Java, a Bing Custom Search API é um serviço web RESTful compatível com a maioria das linguagens de programação. O código-fonte desta amostra está disponível no [GitHub](https://github.com/Azure-Samples/cognitive-services-REST-api-samples/blob/master/java/Search/BingCustomSearchv7.java).

## <a name="prerequisites"></a>Pré-requisitos

- Um caso de pesquisa personalizada Bing. Para obter mais informações, consulte [Quickstart: Crie a sua primeira instância de Pesquisa Personalizada Bing](quick-start.md).

- O mais recente [Kit de Desenvolvimento de Java.](https://www.oracle.com/technetwork/java/javase/downloads/index.html)

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

2. Crie uma classe chamada `CustomSrchJava` , e, em seguida, crie variáveis para a sua chave de subscrição, ponto final de pesquisa personalizado e ID de configuração personalizada de instância de pesquisa. Pode utilizar o ponto final global no seguinte código ou utilizar o ponto final [de subdomínio personalizado](../../cognitive-services/cognitive-services-custom-subdomains.md) apresentado no portal Azure para o seu recurso.
    ```java
    public class CustomSrchJava {
        static String host = "https://api.cognitive.microsoft.com";
        static String path = "/bingcustomsearch/v7.0/search";
        static String subscriptionKey = "YOUR-SUBSCRIPTION-KEY"; 
        static String customConfigId = "YOUR-CUSTOM-CONFIG-ID";
        static String searchTerm = "Microsoft";  
    ...
    ```

3. Crie outra classe nomeada `SearchResults` para conter a resposta da sua instância de Pesquisa Personalizada Bing.

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

4. Crie uma função nomeada `prettify()` para formatar a resposta JSON a partir da API de Pesquisa Personalizada Bing.

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

1. Crie uma função nomeada `SearchWeb()` que envie um pedido e devolva um `SearchResults` objeto. Crie o url de pedido combinando as informações de ID, consulta e ponto final de configuração personalizadas. Adicione a chave de subscrição ao `Ocp-Apim-Subscription-Key` cabeçalho.

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

3. Imprima a resposta JSON.

    ```java
    System.out.println("\nJSON Response:\n");
    System.out.println(prettify(result.jsonResponse));
    ```

4. Executar o programa.
    
## <a name="next-steps"></a>Passos seguintes

> [!div class="nextstepaction"]
> [Construa um aplicativo web de pesquisa personalizada](./tutorials/custom-search-web-page.md)
