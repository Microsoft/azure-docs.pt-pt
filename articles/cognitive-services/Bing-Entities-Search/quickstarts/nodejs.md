---
title: 'Início rápido: Enviar uma solicitação de pesquisa para a API REST do Pesquisa de Entidade do Bing usando o Node. js'
titleSuffix: Azure Cognitive Services
description: Use este guia de início rápido para enviar uma solicitação para a API C#REST do pesquisa de entidade do Bing usando e receber uma resposta JSON.
services: cognitive-services
author: aahill
manager: nitinme
ms.service: cognitive-services
ms.subservice: bing-entity-search
ms.topic: quickstart
ms.date: 02/01/2019
ms.author: aahi
ms.openlocfilehash: 139f05e07060cc6dd958c19930e31b1b44e5d22a
ms.sourcegitcommit: 198c3a585dd2d6f6809a1a25b9a732c0ad4a704f
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 07/23/2019
ms.locfileid: "68424026"
---
# <a name="quickstart-send-a-search-request-to-the-bing-entity-search-rest-api-using-nodejs"></a>Início rápido: Enviar uma solicitação de pesquisa para a API REST do Pesquisa de Entidade do Bing usando o Node. js

Use este guia de início rápido para fazer sua primeira chamada para a API de Pesquisa de Entidade do Bing e exibir a resposta JSON. Esse aplicativo JavaScript simples envia uma consulta de pesquisa de notícias para a API e exibe a resposta. O código fonte deste exemplo está disponível no [GitHub](https://github.com/Azure-Samples/cognitive-services-REST-api-samples/blob/master/nodejs/Search/BingEntitySearchv7.js).

Embora esse aplicativo seja escrito em JavaScript, a API é um serviço Web RESTful compatível com a maioria das linguagens de programação.

## <a name="prerequisites"></a>Pré-requisitos

* A versão mais recente do [Node.js](https://nodejs.org/en/download/).

* A [biblioteca de solicitações do JavaScript](https://github.com/request/request)

[!INCLUDE [cognitive-services-bing-news-search-signup-requirements](../../../../includes/cognitive-services-bing-entity-search-signup-requirements.md)]

## <a name="create-and-initialize-the-application"></a>Criar e inicializar a aplicação

1. Crie um novo ficheiro JavaScript no seu IDE ou editor favorito e defina os requisitos de exatidão e HTTPS.

    ```javaScript
    'use strict';
    let https = require ('https');
    ```

2. Crie variáveis para o ponto de extremidade da API, sua chave de assinatura e consulta de pesquisa.

    ```javascript
    let subscriptionKey = 'ENTER YOUR KEY HERE';
    let host = 'api.cognitive.microsoft.com';
    let path = '/bing/v7.0/entities';
    
    let mkt = 'en-US';
    let q = 'italian restaurant near me';
    ```

3. Acrescente os parâmetros de consulta e de mercado a uma `query`cadeia de caracteres chamada. Certifique-se de codificar por URL sua consulta `encodeURI()`com.
    ```javascript 
    let query = '?mkt=' + mkt + '&q=' + encodeURI(q);
    ```

## <a name="handle-and-parse-the-response"></a>Processar e analisar a resposta

1. Defina uma função chamada `response_handler` que usa uma chamada http, `response`, como um parâmetro. Nessa função, execute as seguintes etapas:

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

    3. Quando um sinalizador de **fim** é sinalizado, analise o JSON e imprima-o.

        ```javascript
        response.on ('end', function () {
        let json = JSON.stringify(JSON.parse(body), null, '  ');
        console.log (json);
        });
        ```

## <a name="send-a-request"></a>Enviar um pedido

1. Crie uma função chamada `Search` para enviar uma solicitação de pesquisa. Nele, execute as etapas a seguir.

   1. Crie um objeto JSON contendo seus parâmetros de solicitação: `Get` use para o método e adicione as informações de host e caminho. Adicione sua chave de assinatura ao `Ocp-Apim-Subscription-Key` cabeçalho. 
   2. Use `https.request()` para enviar a solicitação com o manipulador de resposta criado anteriormente e os parâmetros de pesquisa.
    
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

2. Chame a `Search()` função.

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

## <a name="next-steps"></a>Passos seguintes

> [!div class="nextstepaction"]
> [Criar um aplicativo Web de página única](../tutorial-bing-entities-search-single-page-app.md)

* [O que é o API de Pesquisa de Entidade do Bing?](../overview.md )
* [Referência de API de Pesquisa de Entidade do Bing](https://docs.microsoft.com/rest/api/cognitiveservices-bingsearch/bing-entities-api-v7-reference)
