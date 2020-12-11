---
title: 'Quickstart: Enviar um pedido de pesquisa à API REST utilizando Node.js - Pesquisa de Entidade Bing'
titleSuffix: Azure Cognitive Services
description: Utilize este quickstart para enviar um pedido à API de Pesquisa de Entidade Bing usando Node.js e receba uma resposta JSON.
services: cognitive-services
author: aahill
manager: nitinme
ms.service: cognitive-services
ms.subservice: bing-entity-search
ms.topic: quickstart
ms.date: 05/08/2020
ms.author: aahi
ms.custom: devx-track-js
ms.openlocfilehash: b9311329ea4115d49f36dd7d39782bbd748a356b
ms.sourcegitcommit: 6172a6ae13d7062a0a5e00ff411fd363b5c38597
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 12/11/2020
ms.locfileid: "97106109"
---
# <a name="quickstart-send-a-search-request-to-the-bing-entity-search-rest-api-using-nodejs"></a>Quickstart: Envie um pedido de pesquisa para a API de pesquisa de entidade bing usando Node.js

> [!WARNING]
> As APIs de Pesquisa de Bing estão a mover-se dos Serviços Cognitivos para os Serviços de Pesquisa Bing. A partir **de 30 de outubro de 2020,** quaisquer novos casos de Bing Search devem ser adquir-se na sequência do processo [aqui](/bing/search-apis/bing-web-search/create-bing-search-service-resource)documentado.
> Bing Search APIs aforados usando Serviços Cognitivos será suportado durante os próximos três anos ou até o final do seu Contrato de Empresa, o que acontecer primeiro.
> Para obter instruções de migração, consulte [os Serviços de Busca Bing.](/bing/search-apis/bing-web-search/create-bing-search-service-resource)

Utilize este quickstart para fazer a sua primeira chamada para a API de Pesquisa de Entidade Bing e veja a resposta JSON. Esta simples aplicação JavaScript envia uma consulta de pesquisa de notícias para a API e exibe a resposta. O código-fonte desta amostra está disponível no [GitHub](https://github.com/Azure-Samples/cognitive-services-REST-api-samples/blob/master/nodejs/Search/BingEntitySearchv7.js).

Embora esta aplicação esteja escrita em JavaScript, a API é um serviço Web RESTful compatível com a maioria das linguagens de programação.

## <a name="prerequisites"></a>Pré-requisitos

* A versão mais recente do [Node.js](https://nodejs.org/en/download/).

* A [Biblioteca de Pedidos JavaScript.](https://github.com/request/request)

[!INCLUDE [cognitive-services-bing-news-search-signup-requirements](../../../../includes/cognitive-services-bing-entity-search-signup-requirements.md)]

## <a name="create-and-initialize-the-application"></a>Criar e inicializar a aplicação

1. Crie um novo ficheiro JavaScript no seu IDE ou editor favorito e desaprove os requisitos de rigor e HTTPS.

    ```javaScript
    'use strict';
    let https = require ('https');
    ```

2. Crie variáveis para o ponto final da API, a sua chave de subscrição e consulta de pesquisa. Pode utilizar o ponto final global no seguinte código ou utilizar o ponto final [de subdomínio personalizado](../../../cognitive-services/cognitive-services-custom-subdomains.md) apresentado no portal Azure para o seu recurso.

    ```javascript
    let subscriptionKey = 'ENTER YOUR KEY HERE';
    let host = 'api.cognitive.microsoft.com';
    let path = '/bing/v7.0/entities';
    
    let mkt = 'en-US';
    let q = 'italian restaurant near me';
    ```

3. Anexar o seu mercado e consultar os parâmetros a uma cadeia chamada `query` . Certifique-se de codificar a sua consulta com `encodeURI()` .
    ```javascript 
    let query = '?mkt=' + mkt + '&q=' + encodeURI(q);
    ```

## <a name="handle-and-parse-the-response"></a>Processar e analisar a resposta

1. Defina uma função nomeada `response_handler()` que recebe uma chamada HTTP, como `response` parâmetro. 

2. Dentro desta função, definir uma variável para conter o corpo da resposta JSON.  
    ```javascript
    let response_handler = function (response) {
        let body = '';
    };
    ```

3. Guarde o corpo da resposta quando a `data` bandeira for chamada.
    ```javascript
    response.on('data', function (d) {
        body += d;
    });
    ```

4. Quando uma `end` bandeira é sinalizada, analise o JSON e imprima-a.

    ```javascript
    response.on ('end', function () {
    let json = JSON.stringify(JSON.parse(body), null, '  ');
    console.log (json);
    });
    ```

## <a name="send-a-request"></a>Enviar um pedido

1. Crie uma função chamada `Search()` para enviar um pedido de pesquisa. Nele, execute os seguintes passos:

2. Dentro desta função, crie um objeto JSON contendo os parâmetros de pedido. Use `Get` para o método e adicione as informações do anfitrião e do caminho. Adicione a chave de subscrição ao `Ocp-Apim-Subscription-Key` cabeçalho. 

3. Utilize `https.request()` para enviar o pedido com o manipulador de resposta criado anteriormente e os seus parâmetros de pesquisa.
    
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

2. Ligue para a `Search()` função.

## <a name="example-json-response"></a>Exemplo JSON resposta

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
> [Criar uma aplicação web de página única](../tutorial-bing-entities-search-single-page-app.md)

* [O que é a API de Pesquisa de Entidade Bing?](../overview.md )
* [Bing Entidade Pesquisa referência API](/rest/api/cognitiveservices-bingsearch/bing-entities-api-v7-reference).
