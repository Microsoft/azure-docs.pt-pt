---
title: 'Início rápido: verificar a ortografia com a API REST e o Node. js-Verificação Ortográfica do Bing'
titleSuffix: Azure Cognitive Services
description: Comece a usar a API REST do Verificação Ortográfica do Bing para verificar a ortografia e a gramática com este guia de início rápido.
services: cognitive-services
author: aahill
manager: nitinme
ms.service: cognitive-services
ms.subservice: bing-spell-check
ms.topic: quickstart
ms.date: 12/16/2019
ms.author: aahi
ms.openlocfilehash: 69c391e6c3f93a998ade7c5721a528d895f8df76
ms.sourcegitcommit: f4f626d6e92174086c530ed9bf3ccbe058639081
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 12/25/2019
ms.locfileid: "75382864"
---
# <a name="quickstart-check-spelling-with-the-bing-spell-check-rest-api-and-nodejs"></a>Início rápido: verificar a ortografia com a API REST do Verificação Ortográfica do Bing e o Node. js

Use este guia de início rápido para fazer sua primeira chamada para a API REST do Verificação Ortográfica do Bing. Esse aplicativo de nó simples envia uma solicitação para a API e retorna uma lista de palavras que ela não reconheceu, seguida de correções sugeridas. Embora esse aplicativo seja escrito em node. js, a API é um serviço Web RESTful compatível com a maioria das linguagens de programação. O código-fonte para este aplicativo está disponível no [GitHub](https://github.com/Azure-Samples/cognitive-services-REST-api-samples/blob/master/nodejs/Search/BingSpellCheckv7.js).

## <a name="prerequisites"></a>Pré-requisitos

* [Node. js 6](https://nodejs.org/en/download/) ou posterior.

[!INCLUDE [cognitive-services-bing-spell-check-signup-requirements](../../../../includes/cognitive-services-bing-spell-check-signup-requirements.md)]


## <a name="create-and-initialize-a-project"></a>Criar e inicializar um projeto

1. Crie um novo arquivo JavaScript em seu IDE ou editor favorito. Defina a restrição e exija `https`. Em seguida, crie variáveis para o host do seu ponto de extremidade de API, o caminho e sua chave de assinatura. Você pode usar o ponto de extremidade global abaixo ou o ponto de extremidade de [subdomínio personalizado](../../../cognitive-services/cognitive-services-custom-subdomains.md) exibido no portal do Azure para seu recurso.

    ```javascript
    'use strict';
    let https = require ('https');

    let host = 'api.cognitive.microsoft.com';
    let path = '/bing/v7.0/spellcheck';
    let key = '<ENTER-KEY-HERE>';
    ```

2. Crie variáveis para os parâmetros de pesquisa e o texto que você deseja verificar. Acrescente seu código de mercado após `mkt=`. O código do mercado é o país do qual você faz a solicitação. Além disso, acrescente seu modo de verificação ortográfica após `&mode=`. O modo é `proof` (captura a maioria dos erros de ortografia/gramática) ou `spell` (captura a maior parte da ortografia, mas não a quantidade de erros gramaticais).

    ```javascript
    let mkt = "en-US";
    let mode = "proof";
    let text = "Hollo, wrld!";
    let query_string = "?mkt=" + mkt + "&mode=" + mode;
    ```

## <a name="create-the-request-parameters"></a>Criar os parâmetros de solicitação

Crie seus parâmetros de solicitação criando um novo objeto com um método `POST`. Adicione seu caminho acrescentando o caminho do ponto de extremidade e a cadeia de caracteres de consulta. Adicione sua chave de assinatura ao cabeçalho `Ocp-Apim-Subscription-Key`.

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

Crie uma função chamada `response_handler` para pegar a resposta JSON da API e imprimi-la. Crie uma variável para o corpo da resposta. Acrescente a resposta quando um sinalizador de `data` for recebido, usando `response.on()`. Quando um sinalizador de `end` for recebido, imprima o corpo JSON no console.

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

## <a name="send-the-request"></a>Enviar a solicitação

Chame a API usando `https.request()` com os parâmetros de solicitação e o manipulador de resposta. Grave o texto na API e Finalize a solicitação posteriormente.

```javascript
let req = https.request (request_params, response_handler);
req.write ("text=" + text);
req.end ();
```


## <a name="run-the-application"></a>Executar a aplicação

Compile e execute seu projeto.

Se você estiver usando a linha de comando, use os comandos a seguir para compilar e executar o aplicativo.

```bash
node <FILE_NAME>.js
```


## <a name="example-json-response"></a>Exemplo de resposta JSON

O JSON devolve uma resposta de êxito, conforme apresentado no exemplo seguinte:

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

## <a name="next-steps"></a>Passos seguintes

> [!div class="nextstepaction"]
> [Criar um aplicativo Web de página única](../tutorials/spellcheck.md)

- [O que é a API de Verificação Ortográfica do Bing?](../overview.md)
- [Referência da API de Verificação de Ortografia do Bing v7](https://docs.microsoft.com/rest/api/cognitiveservices-bingsearch/bing-spell-check-api-v7-reference)
