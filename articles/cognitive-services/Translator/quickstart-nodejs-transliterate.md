---
title: 'Guia de Início Rápido: Converter script de texto – Tradução de Texto, Node.js'
titleSuffix: Azure Cognitive Services
description: Neste guia de início rápido, irá converter texto de um idioma de um script noutro através da API de Texto do Microsoft Translator com Node.js.
services: cognitive-services
author: noellelacharite
manager: cgronlun
ms.service: cognitive-services
ms.component: translator-text
ms.topic: quickstart
ms.date: 06/21/2018
ms.author: nolachar
ms.openlocfilehash: 53ebcd6c37141a783303dd71666d5c8d4805ca49
ms.sourcegitcommit: f10653b10c2ad745f446b54a31664b7d9f9253fe
ms.translationtype: HT
ms.contentlocale: pt-PT
ms.lasthandoff: 09/18/2018
ms.locfileid: "46127701"
---
# <a name="quickstart-transliterate-text-with-nodejs"></a>Guia de Início Rápido: Transliterar texto com Node.js

Neste guia de introdução, irá converter texto de um idioma de um script para outro através da API de Texto do Microsoft Translator.

## <a name="prerequisites"></a>Pré-requisitos

Irá precisar do [Node.js 6](https://nodejs.org/en/download/) para executar este código.

Para utilizar a API de Texto do Microsoft Translator, também precisa de uma chave de subscrição, veja [Como inscrever-se na API de Texto do Microsoft Translator](translator-text-how-to-signup.md).

## <a name="transliterate-request"></a>Pedido de transliteração

Os passos seguintes convertem texto num idioma de um script para outro script com o método [Transliteração](./reference/v3-0-transliterate.md).

1. Crie um novo projeto Node.js no seu editor de código preferido.
2. Adicione o código indicado abaixo.
3. Substitua o valor `subscriptionKey` por uma chave de acesso válida para a sua subscrição.
4. Execute o programa.

```javascript
'use strict';

let fs = require ('fs');
let https = require ('https');

// **********************************************
// *** Update or verify the following values. ***
// **********************************************

// Replace the subscriptionKey string value with your valid subscription key.
let subscriptionKey = 'ENTER KEY HERE';

let host = 'api.cognitive.microsofttranslator.com';
let path = '/transliterate?api-version=3.0';

// Transliterate text in Japanese from Japanese script (i.e. Hiragana/Katakana/Kanji) to Latin script.
let params = '&language=ja&fromScript=jpan&toScript=latn';

// Transliterate "good afternoon".
let text = 'こんにちは';

let response_handler = function (response) {
    let body = '';
    response.on ('data', function (d) {
        body += d;
    });
    response.on ('end', function () {
        let json = JSON.stringify(JSON.parse(body), null, 4);
        console.log(json);
    });
    response.on ('error', function (e) {
        console.log ('Error: ' + e.message);
    });
};

let get_guid = function () {
  return 'xxxxxxxx-xxxx-4xxx-yxxx-xxxxxxxxxxxx'.replace(/[xy]/g, function(c) {
    var r = Math.random() * 16 | 0, v = c == 'x' ? r : (r & 0x3 | 0x8);
    return v.toString(16);
  });
}

let Transliterate = function (content) {
    let request_params = {
        method : 'POST',
        hostname : host,
        path : path + params,
        headers : {
            'Content-Type' : 'application/json',
            'Ocp-Apim-Subscription-Key' : subscriptionKey,
            'X-ClientTraceId' : get_guid (),
        }
    };

    let req = https.request (request_params, response_handler);
    req.write (content);
    req.end ();
}

let content = JSON.stringify ([{'Text' : text}]);

Transliterate (content);
```

## <a name="transliterate-response"></a>Resposta de transliteração

É devolvida uma resposta com êxito em JSON, tal como apresentado no exemplo seguinte:

```json
[
  {
    "text": "konnnichiha",
    "script": "latn"
  }
]
```

## <a name="next-steps"></a>Passos seguintes

Explore o código de exemplo para este guia de início rápido e outros, incluindo a tradução e a identificação do idioma, assim como outros projetos de Tradução de Texto no GitHub.

> [!div class="nextstepaction"]
> [Explorar exemplos do Node.js no GitHub](https://aka.ms/TranslatorGitHub?type=&language=javascript)
