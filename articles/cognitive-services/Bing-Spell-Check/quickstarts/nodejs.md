---
title: 'Início rápido: Verifique a ortografia com a API REST de verificação ortográfica do Bing e o node. js'
titlesuffix: Azure Cognitive Services
description: Começar a utilizar a API de REST de verificação do Bing ortográfica para verificar a ortografia e gramática.
services: cognitive-services
author: aahill
manager: nitinme
ms.service: cognitive-services
ms.subservice: bing-spell-check
ms.topic: quickstart
ms.date: 04/02/2019
ms.author: aahill
ms.openlocfilehash: ba56f6d68df34fc9249d02c8aba9155ed41b5be8
ms.sourcegitcommit: 3d4121badd265e99d1177a7c78edfa55ed7a9626
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 05/30/2019
ms.locfileid: "66388451"
---
# <a name="quickstart-check-spelling-with-the-bing-spell-check-rest-api-and-nodejs"></a>Início rápido: Verifique a ortografia com a API REST de verificação ortográfica do Bing e o node. js

Utilize este guia de introdução para efetuar a primeira chamada à API do REST Bing de verificação de ortográfica. Este nó simple aplicação envia um pedido para a API e retorna uma lista de palavras, ele não reconhece, seguido de correção sugerida. Embora esse aplicativo é escrito em node. js, a API é um serviço RESTful Web compatível com a maioria das linguagens de programação. O código-fonte para esta aplicação está disponível no [GitHub](https://github.com/Azure-Samples/cognitive-services-REST-api-samples/blob/master/nodejs/Search/BingSpellCheckv7.js).

## <a name="prerequisites"></a>Pré-requisitos

* [NODE. js 6](https://nodejs.org/en/download/) ou posterior.

[!INCLUDE [cognitive-services-bing-spell-check-signup-requirements](../../../../includes/cognitive-services-bing-spell-check-signup-requirements.md)]


## <a name="create-and-initialize-a-project"></a>Criar e inicializar um projeto

1. Crie um novo ficheiro JavaScript no seu IDE ou editor favorito. Definir o strictness e exigir `https`. Em seguida, crie variáveis para anfitrião do seu ponto final de API, caminho e a chave de subscrição.

    ```javascript
    'use strict';
    let https = require ('https');

    let host = 'api.cognitive.microsoft.com';
    let path = '/bing/v7.0/spellcheck';
    let key = '<ENTER-KEY-HERE>';
    ```

2. Crie variáveis para seus parâmetros de pesquisa e o texto que pretende verificar. Acrescente o código de mercado após `mkt=`. O código de mercado é o país que fizer o pedido do. Além disso, acrescentar o modo de verificação de ortografia após `&mode=`. Modo está `proof` (captura a maioria dos erros de ortografia/gramática) ou `spell` (captura a maioria dos ortografia, mas não tantos erros de gramática).

    ```javascript
    let mkt = "en-US";
    let mode = "proof";
    let text = "Hollo, wrld!";
    let query_string = "?mkt=" + mkt + "&mode=" + mode;
    ```

## <a name="create-the-request-parameters"></a>Criar os parâmetros do pedido

Pode criar os parâmetros do pedido ao criar um novo objeto com um `POST` método. Adicione o seu caminho ao acrescentar o caminho do ponto final e cadeia de consulta. Adicionar a chave de subscrição para o `Ocp-Apim-Subscription-Key` cabeçalho.

```javascript
let request_params = {
   method : 'POST',
   hostname : host,
   path : path + query_string,
   headers : {
   'Content-Type' : 'application/x-www-form-urlencoded',
   'Content-Length' : text.length + 5,
      'Ocp-Apim-Subscription-Key' : key,
   }
};
```

## <a name="create-a-response-handler"></a>Criar um processador de resposta

Criar uma função chamada `response_handler` tirar a resposta JSON da API e imprimi-lo. Crie uma variável para o corpo da resposta. Acrescentar a resposta quando um `data` sinalizador é recebido, usando `response.on()`. Quando um `end` sinalizador é recebido, o corpo JSON para a consola de impressão.

```javascript
let response_handler = function (response) {
    let body = '';
    response.on ('data', function (d) {
        body += d;
    });
    response.on ('end', function () {
        let body_ = JSON.parse (body);
        console.log (body_);
    });
    response.on ('error', function (e) {
        console.log ('Error: ' + e.message);
    });
};
```

## <a name="send-the-request"></a>Enviar o pedido

Chamar a API utilizando `https.request()` com os seus parâmetros de pedido e o manipulador de resposta. Escrever o seu texto para a API e, depois de terminar o pedido.

```javascript
let req = https.request (request_params, response_handler);
req.write ("text=" + text);
req.end ();
```

## <a name="example-json-response"></a>Resposta JSON de exemplo

É devolvida uma resposta com êxito em JSON, tal como é apresentado no exemplo seguinte:

```json
{
   "_type": "SpellCheck",
   "flaggedTokens": [
      {
         "offset": 0,
         "token": "Hollo",
         "type": "UnknownToken",
         "suggestions": [
            {
               "suggestion": "Hello",
               "score": 0.9115257530801
            },
            {
               "suggestion": "Hollow",
               "score": 0.858039839213461
            },
            {
               "suggestion": "Hallo",
               "score": 0.597385084464481
            }
         ]
      },
      {
         "offset": 7,
         "token": "wrld",
         "type": "UnknownToken",
         "suggestions": [
            {
               "suggestion": "world",
               "score": 0.9115257530801
            }
         ]
      }
   ]
}
```

## <a name="next-steps"></a>Passos Seguintes

> [!div class="nextstepaction"]
> [Criar uma aplicação web de página única](../tutorials/spellcheck.md)

- [O que é a API de verificação de ortografia do Bing?](../overview.md)
- [Bing Spell Check API v7 Reference](https://docs.microsoft.com/rest/api/cognitiveservices-bingsearch/bing-spell-check-api-v7-reference) (Referência da API de Verificação de Ortografia do Bing v7)
