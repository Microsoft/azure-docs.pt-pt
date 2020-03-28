---
title: 'Quickstart: Verifique a ortografia com a REST API e Node.js - Bing Spell Check'
titleSuffix: Azure Cognitive Services
description: Comece a usar a API bing spell check REST para verificar a ortografia e a gramática com este arranque rápido.
services: cognitive-services
author: aahill
manager: nitinme
ms.service: cognitive-services
ms.subservice: bing-spell-check
ms.topic: quickstart
ms.date: 12/16/2019
ms.author: aahi
ms.openlocfilehash: 69c391e6c3f93a998ade7c5721a528d895f8df76
ms.sourcegitcommit: 9ee0cbaf3a67f9c7442b79f5ae2e97a4dfc8227b
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/27/2020
ms.locfileid: "75382864"
---
# <a name="quickstart-check-spelling-with-the-bing-spell-check-rest-api-and-nodejs"></a>Quickstart: Verifique a ortografia com o Bing Spell Check REST API e Node.js

Use este quickstart para fazer a sua primeira chamada para a API de Verificação de Feitiços de Bing. Este simples pedido de Nó envia um pedido à API e devolve uma lista de palavras que não reconheceu, seguidas de correções sugeridas. Embora esta aplicação esteja escrita no Node.js, a API é um serviço Web RESTful compatível com a maioria dos idiomas de programação. O código fonte desta aplicação está disponível no [GitHub](https://github.com/Azure-Samples/cognitive-services-REST-api-samples/blob/master/nodejs/Search/BingSpellCheckv7.js).

## <a name="prerequisites"></a>Pré-requisitos

* [Nó.js 6](https://nodejs.org/en/download/) ou mais tarde.

[!INCLUDE [cognitive-services-bing-spell-check-signup-requirements](../../../../includes/cognitive-services-bing-spell-check-signup-requirements.md)]


## <a name="create-and-initialize-a-project"></a>Criar e inicializar um projeto

1. Crie um novo ficheiro JavaScript no seu IDE ou editor favorito. Desaperte a `https`estrito e exija. Em seguida, crie variáveis para o anfitrião, caminho e chave de subscrição do seu ponto final da API. Pode utilizar o ponto final global abaixo, ou o ponto final personalizado do [subdomínio](../../../cognitive-services/cognitive-services-custom-subdomains.md) exibido no portal Azure para o seu recurso.

    ```javascript
    'use strict';
    let https = require ('https');

    let host = 'api.cognitive.microsoft.com';
    let path = '/bing/v7.0/spellcheck';
    let key = '<ENTER-KEY-HERE>';
    ```

2. Crie variáveis para os seus parâmetros de pesquisa e o texto que pretende verificar. Anexar o seu `mkt=`código de mercado depois de . O código de mercado é o país de onde faz o pedido. Além disso, apende `&mode=`o seu modo de verificação ortográfica depois de . O modo `proof` é ou (apanha a `spell` maioria dos erros ortográficos/gramaticais) ou (captura a maioria da ortografia, mas não tantos erros gramaticais).

    ```javascript
    let mkt = "en-US";
    let mode = "proof";
    let text = "Hollo, wrld!";
    let query_string = "?mkt=" + mkt + "&mode=" + mode;
    ```

## <a name="create-the-request-parameters"></a>Criar os parâmetros de pedido

Crie os parâmetros de pedido `POST` criando um novo objeto com um método. Adicione o seu caminho, apertou o seu caminho final e a corda de consulta. Adicione a sua `Ocp-Apim-Subscription-Key` chave de subscrição ao cabeçalho.

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

Crie uma `response_handler` função chamada para retirar a resposta JSON da API, e imprimi-la. Criar uma variável para o corpo de resposta. Anexar a resposta `data` quando uma `response.on()`bandeira é recebida, utilizando . Quando `end` uma bandeira for recebida, imprima o corpo JSON para a consola.

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

Ligue para a `https.request()` API utilizando os parâmetros do seu pedido e o manipulador de resposta. Escreva o seu texto para a API e termine o pedido depois.

```javascript
let req = https.request (request_params, response_handler);
req.write ("text=" + text);
req.end ();
```


## <a name="run-the-application"></a>Executar a aplicação

Construa e gereno seu projeto.

Se estiver a utilizar a linha de comando, utilize os seguintes comandos para construir e executar a aplicação.

```bash
node <FILE_NAME>.js
```


## <a name="example-json-response"></a>Exemplo resposta JSON

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

## <a name="next-steps"></a>Passos seguintes

> [!div class="nextstepaction"]
> [Criar uma aplicação Web de página única](../tutorials/spellcheck.md)

- [O que é a API de Verificação Ortográfica do Bing?](../overview.md)
- [Referência da API de Verificação de Ortografia do Bing v7](https://docs.microsoft.com/rest/api/cognitiveservices-bingsearch/bing-spell-check-api-v7-reference)
