---
title: 'Quickstart: Send a search request to the REST API using Node.js - Bing Entity Search'
titleSuffix: Azure Cognitive Services
description: Use this quickstart to send a request to the Bing Entity Search REST API using C#, and receive a JSON response.
services: cognitive-services
author: aahill
manager: nitinme
ms.service: cognitive-services
ms.subservice: bing-entity-search
ms.topic: quickstart
ms.date: 07/24/2019
ms.author: aahi
ms.openlocfilehash: 48d2b36e35a2e0b41b1202beda9944339dc7530c
ms.sourcegitcommit: b77e97709663c0c9f84d95c1f0578fcfcb3b2a6c
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 11/22/2019
ms.locfileid: "74327118"
---
# <a name="quickstart-send-a-search-request-to-the-bing-entity-search-rest-api-using-nodejs"></a>Quickstart: Send a search request to the Bing Entity Search REST API using Node.js

Use this quickstart to make your first call to the Bing Entity Search API and view the JSON response. This simple JavaScript application sends a news search query to the API, and displays the response. O código fonte deste exemplo está disponível no [GitHub](https://github.com/Azure-Samples/cognitive-services-REST-api-samples/blob/master/nodejs/Search/BingEntitySearchv7.js).

While this application is written in JavaScript, the API is a RESTful Web service compatible with most programming languages.

## <a name="prerequisites"></a>Pré-requisitos

* A versão mais recente do [Node.js](https://nodejs.org/en/download/).

* The [JavaScript Request Library](https://github.com/request/request)

[!INCLUDE [cognitive-services-bing-news-search-signup-requirements](../../../../includes/cognitive-services-bing-entity-search-signup-requirements.md)]

## <a name="create-and-initialize-the-application"></a>Criar e inicializar a aplicação

1. Crie um novo ficheiro JavaScript no seu IDE ou editor favorito e defina os requisitos de exatidão e HTTPS.

    ```javaScript
    'use strict';
    let https = require ('https');
    ```

2. Create variables for the API endpoint, your subscription key, and search query.

    ```javascript
    let subscriptionKey = 'ENTER YOUR KEY HERE';
    let host = 'api.cognitive.microsoft.com';
    let path = '/bing/v7.0/entities';
    
    let mkt = 'en-US';
    let q = 'italian restaurant near me';
    ```

3. Append your market and query parameters to a string called `query`. Be sure to url-encode your query with `encodeURI()`.
    ```javascript 
    let query = '?mkt=' + mkt + '&q=' + encodeURI(q);
    ```

## <a name="handle-and-parse-the-response"></a>Processar e analisar a resposta

1. Define a function named `response_handler` that takes an HTTP call, `response`, as a parameter. Within this function, perform the following steps:

    1. Defina uma variável para incluir o corpo da resposta JSON.  
        ```javascript
        let response_handler = function (response) {
            let body = '';
        };
        ```

    2. Armazene o corpo da resposta quando o sinalizador **data** for chamado
        ```javascript
        response.on('data', function (d) {
            body += d;
        });
        ```

    3. When an **end** flag is signaled, parse the JSON, and print it.

        ```javascript
        response.on ('end', function () {
        let json = JSON.stringify(JSON.parse(body), null, '  ');
        console.log (json);
        });
        ```

## <a name="send-a-request"></a>Enviar um pedido

1. Create a function called `Search` to send a search request. In it, perform the following steps.

   1. Create a JSON object containing your request parameters: use `Get` for the method, and add your host and path information. Add your subscription key to the `Ocp-Apim-Subscription-Key` header. 
   2. Use `https.request()` to send the request with the response handler created earlier, and your search parameters.
    
      ```javascript
      let Search = function () {
       let request_params = {
           method : 'GET',
           hostname : host,
           path : path + query,
           headers : {
               'Ocp-Apim-Subscription-Key' : subscriptionKey,
           }
       };
    
       let req = https.request (request_params, response_handler);
       req.end ();
      }
      ```

2. Call the `Search()` function.

## <a name="example-json-response"></a>Example JSON response

O JSON devolve uma resposta de êxito, conforme apresentado no exemplo seguinte: 

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

## <a name="next-steps"></a>Passos seguintes

> [!div class="nextstepaction"]
> [Build a single-page web app](../tutorial-bing-entities-search-single-page-app.md)

* [What is the Bing Entity Search API?](../overview.md )
* [Bing Entity Search API Reference](https://docs.microsoft.com/rest/api/cognitiveservices-bingsearch/bing-entities-api-v7-reference)
