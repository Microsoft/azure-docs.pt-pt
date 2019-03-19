---
title: 'Início rápido: Enviar um pedido de pesquisa para a API de REST do Search do Bing entidade com Java'
titlesuffix: Azure Cognitive Services
description: Utilize este guia de introdução para enviar um pedido para a API de REST do Search do Bing entidade com Java e receber uma resposta JSON.
services: cognitive-services
author: aahill
manager: nitinme
ms.service: cognitive-services
ms.subservice: bing-entity-search
ms.topic: quickstart
ms.date: 02/01/2019
ms.author: aahi
ms.openlocfilehash: d9001a10afdb92e2ebe469e3beb080c667e8559b
ms.sourcegitcommit: 2d0fb4f3fc8086d61e2d8e506d5c2b930ba525a7
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/18/2019
ms.locfileid: "58097342"
---
# <a name="quickstart-send-a-search-request-to-the-bing-entity-search-rest-api-using-java"></a>Início rápido: Enviar um pedido de pesquisa para a API de REST do Search do Bing entidade com Java

Utilize este guia de introdução para efetuar a primeira chamada para a API de pesquisa de entidades do Bing e ver a resposta JSON. Esta aplicação de Java simple envia uma consulta de pesquisa de notícias para a API e apresenta a resposta.

Embora esta aplicação seja escrita em Java, a API é um serviço Web RESTful compatível com a maioria das linguagens de programação.

## <a name="prerequisites"></a>Pré-requisitos

* O [Kit(JDK) de desenvolvimento do Java](https://www.oracle.com/technetwork/java/javase/downloads/)
* A [Biblioteca de Gson](https://github.com/google/gson)


[!INCLUDE [cognitive-services-bing-news-search-signup-requirements](../../../../includes/cognitive-services-bing-entity-search-signup-requirements.md)]

## <a name="create-and-initialize-a-project"></a>Criar e inicializar um projeto

1. Crie um novo projeto Java no seu IDE ou editor favorito e importe as seguintes bibliotecas.

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

2. Crie uma nova classe, as variáveis para o ponto final da API, a chave de subscrição e uma consulta de pesquisa.

   ```java
   public class EntitySearch {

      static String subscriptionKey = "ENTER KEY HERE";
    
        static String host = "https://api.cognitive.microsoft.com";
        static String path = "/bing/v7.0/entities";
    
        static String mkt = "en-US";
        static String query = "italian restaurant near me";
   //...
    
   ```

## <a name="construct-a-search-request-string"></a>Construir uma cadeia de caracteres de solicitação de pesquisa

1. Criar uma função chamada `search()` que retorna um JSON `String`. URL-codificar a consulta de pesquisa e adicioná-lo a uma cadeia de caracteres de parâmetros com `&q=`. Adicionar seu mercado na cadeia de caracteres com `?mkt=`.
 
2. Crie um objeto de URL com suas cadeias de caracteres de anfitrião, caminho e parâmetros.
    
    ```java
    //...
    public static String search () throws Exception {
        String encoded_query = URLEncoder.encode (query, "UTF-8");
        String params = "?mkt=" + mkt + "&q=" + encoded_query;
        URL url = new URL (host + path + params);
    //...
    ```
      
## <a name="send-a-search-request-and-receive-a-response"></a>Enviar um pedido de pesquisa e receber uma resposta

1. Na `search()` função criada acima, crie uma nova `HttpsURLConnection` objeto com `url.openCOnnection()`. Definir o método de pedido como `GET`e adicione a chave de subscrição para o `Ocp-Apim-Subscription-Key` cabeçalho.

    ```java
    //...
    HttpsURLConnection connection = (HttpsURLConnection) url.openConnection();
    connection.setRequestMethod("GET");
    connection.setRequestProperty("Ocp-Apim-Subscription-Key", subscriptionKey);
    connection.setDoOutput(true);
    //...
    ```

2. Criar um novo `StringBuilder`. Utilizar um novo `InputStreamReader` como um parâmetro ao instanciar `BufferedReader` para ler a resposta de API.  
    
    ```java
    //...
    StringBuilder response = new StringBuilder ();
    BufferedReader in = new BufferedReader(
        new InputStreamReader(connection.getInputStream()));
    //...
    ```

3. Criar uma `String` objeto para armazenar a resposta do `BufferedReader`. Iterar através do mesmo e acrescentar cada linha na cadeia de caracteres. Em seguida, feche o leitor e retornar a resposta. 
    
    ```java
    String line;
    
    while ((line = in.readLine()) != null) {
      response.append(line);
    }
    in.close();
    
    return response.toString();
    ```

## <a name="format-the-json-response"></a>Formatar a resposta JSON

1. Criar uma nova função chamada `prettify` para formatar a resposta JSON. Criar uma nova `JsonParser`e chamar `parse()` no texto json e armazená-lo como um objeto JSON. 

2. Utilize a biblioteca de Gson para criar uma nova `GsonBuilder()`e utilizar `setPrettyPrinting().create()` para formatar o json. Em seguida, retorná-lo.    
  
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

## <a name="call-the-search-function"></a>Chamar a função de pesquisa

1. Do método principal de seu projeto, chame `search()`e utilizar `prettify()` formatar o texto.
    
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

## <a name="example-json-response"></a>Resposta JSON de exemplo

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

[Voltar ao início](#main)

## <a name="next-steps"></a>Passos Seguintes

> [!div class="nextstepaction"]
> [Criar uma aplicação web de página única](../tutorial-bing-entities-search-single-page-app.md)

* [O que é a API de pesquisa de entidades do Bing?](../overview.md )
* [Referência de API de pesquisa de entidades do Bing](https://docs.microsoft.com/rest/api/cognitiveservices/bing-entities-api-v7-reference)
