---
title: 'Início rápido: Enviar um pedido de pesquisa para a API de REST do Search do Bing entidade com node. js'
titlesuffix: Azure Cognitive Services
description: Utilize este guia de introdução para enviar um pedido para utilizar a API de REST de pesquisa de entidades do Bing C#e receber uma resposta JSON.
services: cognitive-services
author: aahill
manager: nitinme
ms.service: cognitive-services
ms.subservice: bing-entity-search
ms.topic: quickstart
ms.date: 02/01/2019
ms.author: aahi
ms.openlocfilehash: 8007d576a6b896f12423087cfd4a483d9171abc5
ms.sourcegitcommit: 5839af386c5a2ad46aaaeb90a13065ef94e61e74
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/19/2019
ms.locfileid: "58104374"
---
# <a name="quickstart-send-a-search-request-to-the-bing-entity-search-rest-api-using-nodejs"></a>Início rápido: Enviar um pedido de pesquisa para a API de REST do Search do Bing entidade com node. js

Utilize este guia de introdução para efetuar a primeira chamada para a API de pesquisa de entidades do Bing e ver a resposta JSON. Esta aplicação simple de JavaScript envia uma consulta de pesquisa de notícias para a API e apresenta a resposta. O código fonte deste exemplo está disponível no [GitHub](https://github.com/Azure-Samples/cognitive-services-REST-api-samples/blob/master/nodejs/Search/BingEntitySearchv7.js).

Embora esse aplicativo é escrito em JavaScript, a API é um serviço RESTful Web compatível com a maioria das linguagens de programação.

## <a name="prerequisites"></a>Pré-requisitos

* A versão mais recente do [Node.js](https://nodejs.org/en/download/).

* O [biblioteca de pedido de JavaScript](https://github.com/request/request)

[!INCLUDE [cognitive-services-bing-news-search-signup-requirements](../../../../includes/cognitive-services-bing-entity-search-signup-requirements.md)]

## <a name="create-and-initialize-the-application"></a>Criar e inicializar a aplicação

1. Crie um novo ficheiro JavaScript no seu IDE ou editor favorito e defina os requisitos de exatidão e HTTPS.

    ```javaScript
    'use strict';
    let https = require ('https');
    ```

2. Crie variáveis para o ponto final da API, sua chave de subscrição e a consulta de pesquisa.

    ```javascript
    let subscriptionKey = 'ENTER YOUR KEY HERE';
    let host = 'api.cognitive.microsoft.com';
    let path = '/bing/v7.0/entities';
    
    let mkt = 'en-US';
    let q = 'italian restaurant near me';
    ```

3. Acrescentar os parâmetros do mercado e a consulta a uma cadeia de caracteres chamada `query`. Certifique-se de que a url-codificar a consulta com `encodeURI()`.
    ```javascript 
    let query = '?mkt=' + mkt + '&q=' + encodeURI(q);
    ```

## <a name="handle-and-parse-the-response"></a>Processar e analisar a resposta

1. definir uma função chamada `response_handler` que assume uma chamada HTTP, `response`, como um parâmetro. dentro desta função, execute os seguintes passos:

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

    3. Quando um **final** sinalizador é sinalizado, analisar o JSON e imprimi-lo.

        ```javascript
        response.on ('end', function () {
        let json = JSON.stringify(JSON.parse(body), null, '  ');
        console.log (json);
        });
        ```

## <a name="send-a-request"></a>Enviar um pedido

1. Criar uma função chamada `Search` para enviar uma solicitação de pesquisa. Nela, execute os seguintes passos.

   1. Criar um objeto JSON que contém os parâmetros do pedido: utilizar `Get` para o método e adicione as suas informações de anfitrião e caminho. Adicionar a chave de subscrição para o `Ocp-Apim-Subscription-Key` cabeçalho. 
   2. Utilize `https.request()` para enviar o pedido com o manipulador de resposta criado anteriormente e seus parâmetros de pesquisa.
    
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

2. Chamar o `Search()` função.

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

## <a name="next-steps"></a>Passos Seguintes

> [!div class="nextstepaction"]
> [Criar uma aplicação web de página única](../tutorial-bing-entities-search-single-page-app.md)

* [O que é a API de pesquisa de entidades do Bing?](../overview.md )
* [Referência de API de pesquisa de entidades do Bing](https://docs.microsoft.com/rest/api/cognitiveservices/bing-entities-api-v7-reference)
