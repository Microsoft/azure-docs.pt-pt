---
title: 'Quickstart: Verifique a ortografia com a API REST e Node.js - Bing Spell Check'
titleSuffix: Azure Cognitive Services
description: Começa a utilizar a API de Verificação ortográfica Bing e Node.js para verificar a ortografia e a gramática.
services: cognitive-services
author: aahill
manager: nitinme
ms.service: cognitive-services
ms.subservice: bing-spell-check
ms.topic: quickstart
ms.date: 05/21/2020
ms.author: aahi
ms.custom: devx-track-js
ms.openlocfilehash: 182ccc972fce40adc88ed66b01c92e778f6479a9
ms.sourcegitcommit: 9eda79ea41c60d58a4ceab63d424d6866b38b82d
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 11/30/2020
ms.locfileid: "96352699"
---
# <a name="quickstart-check-spelling-with-the-bing-spell-check-rest-api-and-nodejs"></a>Quickstart: Verifique a ortografia com a API de verificação ortográfica Bing Spell REST e Node.js

> [!WARNING]
> As APIs de Pesquisa de Bing estão a mover-se dos Serviços Cognitivos para os Serviços de Pesquisa Bing. A partir **de 30 de outubro de 2020,** quaisquer novos casos de Bing Search devem ser adquir-se na sequência do processo [aqui](/bing/search-apis/bing-web-search/create-bing-search-service-resource)documentado.
> Bing Search APIs aforados usando Serviços Cognitivos será suportado durante os próximos três anos ou até o final do seu Contrato de Empresa, o que acontecer primeiro.
> Para obter instruções de migração, consulte [os Serviços de Busca Bing.](/bing/search-apis/bing-web-search/create-bing-search-service-resource)

Utilize este arranque rápido para fazer a sua primeira chamada para a API Bing Spell Check REST. Esta simples aplicação JavaScript envia um pedido à API e devolve uma lista de correções sugeridas. 

Embora esta aplicação esteja escrita em JavaScript, a API é um serviço Web RESTful compatível com a maioria das linguagens de programação. O código fonte desta aplicação está disponível no [GitHub](https://github.com/Azure-Samples/cognitive-services-REST-api-samples/blob/master/nodejs/Search/BingSpellCheckv7.js).

## <a name="prerequisites"></a>Pré-requisitos

* [Node.js 6](https://nodejs.org/en/download/) ou mais tarde.

[!INCLUDE [cognitive-services-bing-spell-check-signup-requirements](../../../../includes/cognitive-services-bing-spell-check-signup-requirements.md)]


## <a name="create-and-initialize-a-project"></a>Criar e inicializar um projeto

1. Crie um novo ficheiro JavaScript no seu IDE ou editor favorito. Desaçam o rigor e `https` exijam. Em seguida, crie variáveis para o anfitrião, caminho e a chave de subscrição do seu ponto final da API. Pode utilizar o ponto final global no seguinte código ou utilizar o ponto final [de subdomínio personalizado](../../../cognitive-services/cognitive-services-custom-subdomains.md) apresentado no portal Azure para o seu recurso.

    ```javascript
    'use strict';
    let https = require ('https');

    let host = 'api.cognitive.microsoft.com';
    let path = '/bing/v7.0/spellcheck';
    let key = '<ENTER-KEY-HERE>';
    ```

2. Crie variáveis para os seus parâmetros de pesquisa e o texto que pretende verificar: 

   1. Atribua o seu código de mercado ao `mkt` parâmetro com o `=` operador. O código de mercado é o código do país/região a que faz o pedido. 

   1. Adicione o `mode` parâmetro com o operador `&` e, em seguida, atribua o modo de verificação ortográfica. O modo pode ser `proof` (apanha a maioria dos erros ortográficos/gramaticais) ou `spell` (apanha a maioria dos erros ortográficos, mas não tantos erros gramaticais).

    ```javascript
    let mkt = "en-US";
    let mode = "proof";
    let text = "Hollo, wrld!";
    let query_string = "?mkt=" + mkt + "&mode=" + mode;
    ```

## <a name="create-the-request-parameters"></a>Criar os parâmetros de pedido

Crie os parâmetros de pedido criando um novo objeto com um `POST` método. Adicione o seu caminho anexando o seu caminho de ponto final e a cadeia de consulta. Em seguida, adicione a sua chave de subscrição ao `Ocp-Apim-Subscription-Key` cabeçalho.

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

Crie uma função chamada `response_handler` para tirar a resposta JSON da API e imprimi-la. Criar uma variável para o corpo de resposta. Apecir a resposta quando uma `data` bandeira é recebida utilizando `response.on()` . Depois de receber uma `end` bandeira, imprima o corpo JSON na consola.

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

Ligue para a API utilizando `https.request()` os parâmetros de pedido e o manipulador de resposta. Escreva o seu texto para a API e, em seguida, termine o pedido.

```javascript
let req = https.request (request_params, response_handler);
req.write ("text=" + text);
req.end ();
```


## <a name="run-the-application"></a>Executar a aplicação

1. Construa e execute o seu projeto.

1. Se estiver a utilizar a linha de comando, utilize o seguinte comando para construir e executar a aplicação:

   ```bash
   node <FILE_NAME>.js
   ```


## <a name="example-json-response"></a>Exemplo JSON resposta

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
> [Criar uma aplicação web de página única](../tutorials/spellcheck.md)

- [O que é a API de Verificação Ortográfica do Bing?](../overview.md)
- [Bing Spell Check Referência V7 da API](/rest/api/cognitiveservices-bingsearch/bing-spell-check-api-v7-reference)