---
title: 'Início rápido: Enviar uma solicitação de pesquisa para a API REST do Pesquisa de Entidade do Bing usando Java'
titleSuffix: Azure Cognitive Services
description: Use este guia de início rápido para enviar uma solicitação para a API REST do Pesquisa de Entidade do Bing usando Java e receba uma resposta JSON.
services: cognitive-services
author: aahill
manager: nitinme
ms.service: cognitive-services
ms.subservice: bing-entity-search
ms.topic: quickstart
ms.date: 07/24/2019
ms.author: aahi
ms.openlocfilehash: 1f7d9620198042adc7f96e7c4aaa269f73870df0
ms.sourcegitcommit: 75a56915dce1c538dc7a921beb4a5305e79d3c7a
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 07/24/2019
ms.locfileid: "68479000"
---
# <a name="quickstart-send-a-search-request-to-the-bing-entity-search-rest-api-using-java"></a>Início rápido: Enviar uma solicitação de pesquisa para a API REST do Pesquisa de Entidade do Bing usando Java

Use este guia de início rápido para fazer sua primeira chamada para a API de Pesquisa de Entidade do Bing e exibir a resposta JSON. Esse aplicativo Java simples envia uma consulta de pesquisa de notícias para a API e exibe a resposta.

Embora esta aplicação seja escrita em Java, a API é um serviço Web RESTful compatível com a maioria das linguagens de programação.

## <a name="prerequisites"></a>Pré-requisitos

* O [Java Development Kit (JDK)](https://www.oracle.com/technetwork/java/javase/downloads/)
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

2. Em uma nova classe, crie variáveis para o ponto de extremidade da API, sua chave de assinatura e uma consulta de pesquisa.

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

1. Crie uma função chamada `search()` que retorna um JSON `String`. Codifique a URL de sua consulta de pesquisa e adicione-a a uma cadeia `&q=`de caracteres de parâmetros com. Adicione seu mercado à cadeia de caracteres `?mkt=`com.
 
2. Crie um objeto URL com suas cadeias de caracteres host, Path e Parameters.
    
    ```java
    //...
    public static String search () throws Exception {
        String encoded_query = URLEncoder.encode (query, "UTF-8");
        String params = "?mkt=" + mkt + "&q=" + encoded_query;
        URL url = new URL (host + path + params);
    //...
    ```
      
## <a name="send-a-search-request-and-receive-a-response"></a>Enviar uma solicitação de pesquisa e receber uma resposta

1. Na função criada acima, crie um novo `HttpsURLConnection` objeto com `url.openCOnnection()`. `search()` Defina o método de solicitação `GET`como e adicione sua chave de assinatura `Ocp-Apim-Subscription-Key` ao cabeçalho.

    ```java
    //...
    HttpsURLConnection connection = (HttpsURLConnection) url.openConnection();
    connection.setRequestMethod("GET");
    connection.setRequestProperty("Ocp-Apim-Subscription-Key", subscriptionKey);
    connection.setDoOutput(true);
    //...
    ```

2. Crie um novo `StringBuilder`. Use um novo `InputStreamReader` como parâmetro ao `BufferedReader` criar uma instância para ler a resposta da API.  
    
    ```java
    //...
    StringBuilder response = new StringBuilder ();
    BufferedReader in = new BufferedReader(
        new InputStreamReader(connection.getInputStream()));
    //...
    ```

3. Crie um `String` objeto para armazenar a resposta `BufferedReader`do. Itere-o e acrescente cada linha à cadeia de caracteres. Em seguida, feche o leitor e retorne a resposta. 
    
    ```java
    String line;
    
    while ((line = in.readLine()) != null) {
      response.append(line);
    }
    in.close();
    
    return response.toString();
    ```

## <a name="format-the-json-response"></a>Formatar a resposta JSON

1. Crie uma nova função chamada `prettify` para formatar a resposta JSON. Crie um novo `JsonParser`e chame `parse()` no texto JSON e armazene-o como um objeto JSON. 

2. Use a biblioteca Gson para criar um novo `GsonBuilder()`e usar `setPrettyPrinting().create()` o para formatar o JSON. Em seguida, retorne-o.    
  
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

1. Do método principal do seu projeto, chame `search()`e use `prettify()` para formatar o texto.
    
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

## <a name="example-json-response"></a>Exemplo de resposta JSON

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

## <a name="next-steps"></a>Passos Seguintes

> [!div class="nextstepaction"]
> [Criar um aplicativo Web de página única](../tutorial-bing-entities-search-single-page-app.md)

* [O que é o API de Pesquisa de Entidade do Bing?](../overview.md )
* [Referência de API de Pesquisa de Entidade do Bing](https://docs.microsoft.com/rest/api/cognitiveservices-bingsearch/bing-entities-api-v7-reference)
