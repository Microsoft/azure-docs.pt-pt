---
title: 'Quickstart: Envie um pedido de pesquisa para a Rest API usando Java - Bing Entity Search'
titleSuffix: Azure Cognitive Services
description: Utilize este quickstart para enviar um pedido para a API de Pesquisa de Entidades Bing usando Java e receber uma resposta JSON.
services: cognitive-services
author: aahill
manager: nitinme
ms.service: cognitive-services
ms.subservice: bing-entity-search
ms.topic: quickstart
ms.date: 05/08/2020
ms.author: aahi
ms.openlocfilehash: 6d4e7297ba0b325aa0045c16620d716ceb1418b4
ms.sourcegitcommit: fdec8e8bdbddcce5b7a0c4ffc6842154220c8b90
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 05/19/2020
ms.locfileid: "83650258"
---
# <a name="quickstart-send-a-search-request-to-the-bing-entity-search-rest-api-using-java"></a>Quickstart: Envie um pedido de pesquisa para a API de pesquisa da entidade bing usando Java

Use este quickstart para fazer a sua primeira chamada para a API de Pesquisa de Entidades Bing e veja a resposta JSON. Esta simples aplicação Java envia uma consulta de pesquisa de notícias à API, e exibe a resposta.

Embora esta aplicação esteja escrita em Java, a API é um serviço Web RESTful compatível com a maioria dos idiomas de programação.

## <a name="prerequisites"></a>Pré-requisitos

* O Kit de [Desenvolvimento java (JDK)](https://www.oracle.com/technetwork/java/javase/downloads/).
* A [biblioteca Gson.](https://github.com/google/gson)


[!INCLUDE [cognitive-services-bing-news-search-signup-requirements](../../../../includes/cognitive-services-bing-entity-search-signup-requirements.md)]

## <a name="create-and-initialize-a-project"></a>Criar e inicializar um projeto

1. Crie um novo projeto Java no seu IDE ou editor favorito, e importe as seguintes bibliotecas:

   ```java
   import java.io.*;
   import java.net.*;
   import java.util.*;
   import javax.net.ssl.HttpsURLConnection;
   import com.google.gson.Gson;
   import com.google.gson.GsonBuilder;
   import com.google.gson.JsonObject;
   import com.google.gson.JsonParser;
   import com.google.gson.Gson;
   import com.google.gson.GsonBuilder;
   import com.google.gson.JsonObject;
   import com.google.gson.JsonParser;
   ```

2. Numa nova classe, crie variáveis para o ponto final da API, a sua chave de subscrição e uma consulta de pesquisa. Pode utilizar o ponto final global no seguinte código ou utilizar o ponto final de [subdomínio personalizado](../../../cognitive-services/cognitive-services-custom-subdomains.md) exibido no portal Azure para o seu recurso.

   ```java
   public class EntitySearch {

      static String subscriptionKey = "ENTER KEY HERE";
    
        static String host = "https://api.cognitive.microsoft.com";
        static String path = "/bing/v7.0/entities";
    
        static String mkt = "en-US";
        static String query = "italian restaurant near me";
   //...
    
   ```

## <a name="construct-a-search-request-string"></a>Construa uma corda de pedido de pesquisa

1. Crie uma função chamada `search()` que retorne um JSON `String` . url-codificar a sua consulta de pesquisa, e adicioná-la a uma cadeia de parâmetros com `&q=` . Adicione o seu mercado à cadeia de parâmetros com `?mkt=` .
 
2. Crie um objeto URL com o seu hospedeiro, caminho e parâmetros cordas.
    
    ```java
    //...
    public static String search () throws Exception {
        String encoded_query = URLEncoder.encode (query, "UTF-8");
        String params = "?mkt=" + mkt + "&q=" + encoded_query;
        URL url = new URL (host + path + params);
    //...
    ```
      
## <a name="send-a-search-request-and-receive-a-response"></a>Envie um pedido de pesquisa e receba uma resposta

1. Na `search()` função acima criada, crie um novo `HttpsURLConnection` objeto com `url.openCOnnection()` . Detete o método de pedido `GET` para, e adicione a sua chave de subscrição ao `Ocp-Apim-Subscription-Key` cabeçalho.

    ```java
    //...
    HttpsURLConnection connection = (HttpsURLConnection) url.openConnection();
    connection.setRequestMethod("GET");
    connection.setRequestProperty("Ocp-Apim-Subscription-Key", subscriptionKey);
    connection.setDoOutput(true);
    //...
    ```

2. Criar um `StringBuilder` novo. Utilize um novo `InputStreamReader` como parâmetro ao instantaneamente para ler a `BufferedReader` resposta da API.  
    
    ```java
    //...
    StringBuilder response = new StringBuilder ();
    BufferedReader in = new BufferedReader(
        new InputStreamReader(connection.getInputStream()));
    //...
    ```

3. Crie um `String` objeto para armazenar a resposta a partir do `BufferedReader` . Iterar através dele, e anexar cada linha à corda. Em seguida, feche o leitor e devolva a resposta. 
    
    ```java
    String line;
    
    while ((line = in.readLine()) != null) {
      response.append(line);
    }
    in.close();
    
    return response.toString();
    ```

## <a name="format-the-json-response"></a>Forforna a resposta JSON

1. Criar uma nova função chamada `prettify` para formatar a resposta JSON. Crie uma nova `JsonParser` , ligue para o texto `parse()` JSON e, em seguida, guarde-o como um objeto JSON. 

2. Use a biblioteca Gson para criar uma nova `GsonBuilder()` , use `setPrettyPrinting().create()` para formatar o JSON e, em seguida, devolvê-lo.    
  
   ```java
   //...
   public static String prettify (String json_text) {
    JsonParser parser = new JsonParser();
    JsonObject json = parser.parse(json_text).getAsJsonObject();
    Gson gson = new GsonBuilder().setPrettyPrinting().create();
    return gson.toJson(json);
   }
   //...
   ```

## <a name="call-the-search-function"></a>Ligue para a função de pesquisa

- A partir do método principal do seu projeto, `search()` ligue, e use `prettify()` para formatar o texto.
    
    ```java
        public static void main(String[] args) {
            try {
                String response = search ();
                System.out.println (prettify (response));
            }
            catch (Exception e) {
                System.out.println (e);
            }
        }
    ```

## <a name="example-json-response"></a>Exemplo resposta JSON

É devolvida uma resposta com êxito em JSON, tal como é apresentado no exemplo seguinte: 

```json
{
  "_type": "SearchResponse",
  "queryContext": {
    "originalQuery": "italian restaurant near me",
    "askUserForLocation": true
  },
  "places": {
    "value": [
      {
        "_type": "LocalBusiness",
        "webSearchUrl": "https://www.bing.com/search?q=sinful+bakery&filters=local...",
        "name": "Liberty's Delightful Sinful Bakery & Cafe",
        "url": "https://www.contoso.com/",
        "entityPresentationInfo": {
          "entityScenario": "ListItem",
          "entityTypeHints": [
            "Place",
            "LocalBusiness"
          ]
        },
        "address": {
          "addressLocality": "Seattle",
          "addressRegion": "WA",
          "postalCode": "98112",
          "addressCountry": "US",
          "neighborhood": "Madison Park"
        },
        "telephone": "(800) 555-1212"
      },

      . . .
      {
        "_type": "Restaurant",
        "webSearchUrl": "https://www.bing.com/search?q=Pickles+and+Preserves...",
        "name": "Munson's Pickles and Preserves Farm",
        "url": "https://www.princi.com/",
        "entityPresentationInfo": {
          "entityScenario": "ListItem",
          "entityTypeHints": [
            "Place",
            "LocalBusiness",
            "Restaurant"
          ]
        },
        "address": {
          "addressLocality": "Seattle",
          "addressRegion": "WA",
          "postalCode": "98101",
          "addressCountry": "US",
          "neighborhood": "Capitol Hill"
        },
        "telephone": "(800) 555-1212"
      },
      
      . . .
    ]
  }
}
```

## <a name="next-steps"></a>Próximos passos

> [!div class="nextstepaction"]
> [Criar uma aplicação web de página única](../tutorial-bing-entities-search-single-page-app.md)

* [O que é a API de Pesquisa de Entidades Bing?](../overview.md)
* [Referência a API](https://docs.microsoft.com/rest/api/cognitiveservices-bingsearch/bing-entities-api-v7-reference)de Pesquisa de Entidades Bing .
