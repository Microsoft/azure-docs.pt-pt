---
title: 'Início Rápido: API de Pesquisa de Entidades do Bing, Java'
titlesuffix: Azure Cognitive Services
description: Obtenha informações e exemplos de código para o ajudar a começar a utilizar rapidamente a API de Pesquisa de Entidades do Bing.
services: cognitive-services
author: aahill
manager: cgronlun
ms.service: cognitive-services
ms.component: bing-entity-search
ms.topic: quickstart
ms.date: 11/28/2017
ms.author: aahi
ms.openlocfilehash: 20a9e9b9c52d45c0f1c933f82e81d2d9a6f73edd
ms.sourcegitcommit: a08d1236f737915817815da299984461cc2ab07e
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 11/26/2018
ms.locfileid: "52313882"
---
# <a name="quickstart-for-bing-entity-search-api-with-java"></a>Início Rápido da API de Pesquisa de Entidades do Bing com Java 

Este artigo mostra-lhe como utilizar a API de [Pesquisa de Entidades do Bing](https://docs.microsoft.com/azure/cognitive-services/bing-entities-search/search-the-web) com Java.

## <a name="prerequisites"></a>Pré-requisitos

Irá precisar do [JDK 7 ou 8](https://aka.ms/azure-jdks) para compilar e executar este código. Pode utilizar um IDE Java se tiver um favorito, mas um editor de texto será suficiente.

Tem de ter uma [conta da API dos Serviços Cognitivos](https://docs.microsoft.com/azure/cognitive-services/cognitive-services-apis-create-account) com a **API de Pesquisa de Entidades do Bing**. A [avaliação gratuita](https://azure.microsoft.com/try/cognitive-services/?api=bing-entity-search-api) é suficiente para este guia de início rápido. Precisa da chave de acesso fornecida quando ativar a avaliação gratuita, ou pode utilizar uma chave de subscrição paga do dashboard do Azure.  Consulte também [dos serviços cognitivos preços - API de pesquisa Bing](https://azure.microsoft.com/pricing/details/cognitive-services/search-api/).

## <a name="search-entities"></a>Pesquisar entidades

Para executar esta aplicação, siga os passos abaixo.

1. Criar um novo projeto de Java no seu IDE favorito.
2. Adicione o código indicado abaixo.
3. Substitua o valor `key` por uma chave de acesso válida para a sua subscrição.
4. Execute o programa.

```java
import java.io.*;
import java.net.*;
import java.util.*;
import javax.net.ssl.HttpsURLConnection;

/*
 * Gson: https://github.com/google/gson
 * Maven info:
 *     groupId: com.google.code.gson
 *     artifactId: gson
 *     version: 2.8.1
 *
 * Once you have compiled or downloaded gson-2.8.1.jar, assuming you have placed it in the
 * same folder as this file (EntitySearch.java), you can compile and run this program at
 * the command line as follows.
 *
 * javac EntitySearch.java -cp .;gson-2.8.1.jar
 * java -cp .;gson-2.8.1.jar EntitySearch
 */
import com.google.gson.Gson;
import com.google.gson.GsonBuilder;
import com.google.gson.JsonObject;
import com.google.gson.JsonParser;

public class EntitySearch {

// **********************************************
// *** Update or verify the following values. ***
// **********************************************

// Replace the subscriptionKey string value with your valid subscription key.
    static String subscriptionKey = "ENTER KEY HERE";

    static String host = "https://api.cognitive.microsoft.com";
    static String path = "/bing/v7.0/entities";

    static String mkt = "en-US";
    static String query = "italian restaurant near me";

    public static String search () throws Exception {
        String encoded_query = URLEncoder.encode (query, "UTF-8");
        String params = "?mkt=" + mkt + "&q=" + encoded_query;
        URL url = new URL (host + path + params);

        HttpsURLConnection connection = (HttpsURLConnection) url.openConnection();
        connection.setRequestMethod("GET");
        connection.setRequestProperty("Ocp-Apim-Subscription-Key", subscriptionKey);
        connection.setDoOutput(true);

        StringBuilder response = new StringBuilder ();
        BufferedReader in = new BufferedReader(
        new InputStreamReader(connection.getInputStream()));
        String line;
        while ((line = in.readLine()) != null) {
            response.append(line);
        }
        in.close();

        return response.toString();
    }

    public static String prettify (String json_text) {
        JsonParser parser = new JsonParser();
        JsonObject json = parser.parse(json_text).getAsJsonObject();
        Gson gson = new GsonBuilder().setPrettyPrinting().create();
        return gson.toJson(json);
    }

    public static void main(String[] args) {
        try {
            String response = search ();
            System.out.println (prettify (response));
        }
        catch (Exception e) {
            System.out.println (e);
        }
    }
}
```

**Resposta**

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
        "url": "http://www.princi.com/",
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

[Voltar ao início](#HOLTop)

## <a name="next-steps"></a>Passos Seguintes

> [!div class="nextstepaction"]
> [Tutorial da Pesquisa de Entidades do Bing](../tutorial-bing-entities-search-single-page-app.md)
> [Descrição geral da Pesquisa de Entidades do Bing](../search-the-web.md )
> [Referência da API](https://docs.microsoft.com/rest/api/cognitiveservices/bing-entities-api-v7-reference)
