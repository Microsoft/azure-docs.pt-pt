---
title: 'Início rápido: sugerir consultas de pesquisa com a API REST do Sugestão Automática do Bing e o Node. js'
titleSuffix: Azure Cognitive Services
description: Saiba como começar a sugerir rapidamente os termos de pesquisa em tempo real com o API de Sugestão Automática do Bing.
services: cognitive-services
author: aahill
manager: nitinme
ms.service: cognitive-services
ms.subservice: bing-autosuggest
ms.topic: quickstart
ms.date: 12/11/2019
ms.author: aahi
ms.openlocfilehash: 9a5cce8102e3a1b3f9beaad8c42b278c56560be9
ms.sourcegitcommit: f4f626d6e92174086c530ed9bf3ccbe058639081
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 12/25/2019
ms.locfileid: "75384939"
---
# <a name="quickstart-suggest-search-queries-with-the-bing-autosuggest-rest-api-and-nodejs"></a>Início rápido: sugerir consultas de pesquisa com a API REST do Sugestão Automática do Bing e o Node. js

Use este guia de início rápido para começar a fazer chamadas para o API de Sugestão Automática do Bing e obter a resposta JSON. Esse aplicativo simples do node. js envia uma consulta de pesquisa parcial para a API e retorna sugestões para pesquisas. Embora esse aplicativo seja escrito em JavaScript, a API é um serviço Web RESTful compatível com a maioria das linguagens de programação. O código-fonte deste exemplo está disponível no [GitHub](https://github.com/Azure-Samples/cognitive-services-REST-api-samples/blob/master/nodejs/Search/BingAutosuggestv7.js)

## <a name="prerequisites"></a>Pré-requisitos

* [Node.js 6](https://nodejs.org/en/download/) ou posterior

[!INCLUDE [cognitive-services-bing-news-search-signup-requirements](../../../../includes/cognitive-services-bing-autosuggest-signup-requirements.md)]

## <a name="create-a-new-application"></a>Criar uma nova aplicação

1. Crie um novo ficheiro JavaScript no seu IDE ou editor favorito e defina os requisitos de exatidão e HTTPS.
    
    ```javascript
    'use strict';
    
    let https = require ('https');
    ```

2. Crie variáveis para o host e o caminho do ponto de extremidade da API, sua chave de assinatura, [código do mercado](https://docs.microsoft.com/rest/api/cognitiveservices-bingsearch/bing-autosuggest-api-v7-reference#market-codes)e um termo de pesquisa. Você pode usar o ponto de extremidade global abaixo ou o ponto de extremidade de [subdomínio personalizado](../../../cognitive-services/cognitive-services-custom-subdomains.md) exibido no portal do Azure para seu recurso.

    ```javascript
    // Replace the subscriptionKey string value with your valid subscription key.
    let subscriptionKey = 'enter key here';
    
    let host = 'api.cognitive.microsoft.com';
    let path = '/bing/v7.0/Suggestions';
    
    let mkt = 'en-US';
    let query = 'sail';
    ```

## <a name="construct-the-search-request-and-query"></a>Crie a consulta e o pedido de pesquisa.

1. Crie uma cadeia de caracteres de parâmetros para a consulta acrescentando o código do mercado ao parâmetro `mkt=` e sua consulta ao parâmetro `q=`.

    ```javascript 
    let params = '?mkt=' + mkt + '&q=' + query;
    ```

2. Crie uma função chamada `get_suggestions()`. Use as variáveis das últimas etapas para formatar uma URL de pesquisa para a solicitação de API. O termo de pesquisa deve ser codificado por URL antes de ser enviado para a API.

    ```javascript
    let get_suggestions = function () {
      let request_params = {
        method : 'GET',
        hostname : host,
        path : path + params,
        headers : {
          'Ocp-Apim-Subscription-Key' : subscriptionKey,
        }
      };
    //...
    }
    ```

    1. Na mesma função, use a biblioteca de solicitações para enviar sua consulta para a API. A função `response_handler` será definida na secção seguinte.
    
        ```javascript
        //...
        let req = https.request(request_params, response_handler);
        req.end();
        ```

## <a name="create-a-search-handler"></a>Criar um manipulador de pesquisa

1. Defina uma função denominada `response_handler` que recebe uma chamada HTTP `response` como um parâmetro. Execute as seguintes etapas nesta função:
    
    1. Defina uma variável para incluir o corpo da resposta JSON.  

        ```javascript
        let response_handler = function (response) {
            let body = '';
        };
        ```

    2. Armazene o corpo da resposta quando o sinalizador **data** for chamado
        
        ```javascript
        response.on ('data', function (d) {
        body += d;
        });
        ```

    3. Quando um sinalizador de **fim** é sinalizado, o usuário `JSON.parse()` e `JSON.stringify()` para imprimir a resposta.
    
        ```javascript
        response.on ('end', function () {
        let body_ = JSON.parse (body);
        let body__ = JSON.stringify (body_, null, '  ');
            console.log (body__);
        });
        response.on ('error', function (e) {
            console.log ('Error: ' + e.message);
        });
        ```

2. Chame `get_suggestions()` para enviar a solicitação para o API de Sugestão Automática do Bing.

## <a name="example-json-response"></a>Exemplo de resposta JSON

É devolvida uma resposta com êxito em JSON, tal como é apresentado no exemplo seguinte: 

```json
{
  "_type": "Suggestions",
  "queryContext": {
    "originalQuery": "sail"
  },
  "suggestionGroups": [
    {
      "name": "Web",
      "searchSuggestions": [
        {
          "url": "https://www.bing.com/cr?IG\u003d2ACC4FE8B02F4AACB9182A6502B0E556\u0026CID\u003d1D546424A4CB64AF2D386F26A5CD6583\u0026rd\u003d1\u0026h\u003dgvtP9TS9NwhajSapY2Se6y1eCbP2fq_GiP2n-cxi6OY\u0026v\u003d1\u0026r\u003dhttps%3a%2f%2fwww.bing.com%2fsearch%3fq%3dsailrite%26FORM%3dUSBAPI\u0026p\u003dDevEx,5003.1",
          "displayText": "sailrite",
          "query": "sailrite",
          "searchKind": "WebSearch"
        },
        {
          "url": "https://www.bing.com/cr?IG\u003d2ACC4FE8B02F4AACB9182A6502B0E556\u0026CID\u003d1D546424A4CB64AF2D386F26A5CD6583\u0026rd\u003d1\u0026h\u003dBTS0G6AakxntIl9rmbDXtk1n6rQpsZZ99aQ7ClE7dTY\u0026v\u003d1\u0026r\u003dhttps%3a%2f%2fwww.bing.com%2fsearch%3fq%3dsail%2bsand%2bpoint%26FORM%3dUSBAPI\u0026p\u003dDevEx,5004.1",
          "displayText": "sail sand point",
          "query": "sail sand point",
          "searchKind": "WebSearch"
        },
        {
          "url": "https://www.bing.com/cr?IG\u003d2ACC4FE8B02F4AACB9182A6502B0E556\u0026CID\u003d1D546424A4CB64AF2D386F26A5CD6583\u0026rd\u003d1\u0026h\u003dc0QOA_j6swCZJy9FxqOwke2KslJE7ZRmMooGClAuCpY\u0026v\u003d1\u0026r\u003dhttps%3a%2f%2fwww.bing.com%2fsearch%3fq%3dsailboats%2bfor%2bsale%26FORM%3dUSBAPI\u0026p\u003dDevEx,5005.1",
          "displayText": "sailboats for sale",
          "query": "sailboats for sale",
          "searchKind": "WebSearch"
        },
        {
          "url": "https://www.bing.com/cr?IG\u003d2ACC4FE8B02F4AACB9182A6502B0E556\u0026CID\u003d1D546424A4CB64AF2D386F26A5CD6583\u0026rd\u003d1\u0026h\u003dmnMdREUH20SepmHQH1zlh9Hy_w7jpOlZFm3KG2R_BoA\u0026v\u003d1\u0026r\u003dhttps%3a%2f%2fwww.bing.com%2fsearch%3fq%3dsailing%2banarchy%26FORM%3dUSBAPI\u0026p\u003dDevEx,5006.1",
          "displayText": "sailing anarchy",
          "query": "sailing anarchy",
          "searchKind": "WebSearch"
        },
        {
          "url": "https://www.bing.com/cr?IG\u003d2ACC4FE8B02F4AACB9182A6502B0E556\u0026CID\u003d1D546424A4CB64AF2D386F26A5CD6583\u0026rd\u003d1\u0026h\u003dWLFO-B1GG5qtBGnoU1Bizz02YKkg5fgAQtHwhXn4z8I\u0026v\u003d1\u0026r\u003dhttps%3a%2f%2fwww.bing.com%2fsearch%3fq%3dsailpoint%26FORM%3dUSBAPI\u0026p\u003dDevEx,5007.1",
          "displayText": "sailpoint",
          "query": "sailpoint",
          "searchKind": "WebSearch"
        },
        {
          "url": "https://www.bing.com/cr?IG\u003d2ACC4FE8B02F4AACB9182A6502B0E556\u0026CID\u003d1D546424A4CB64AF2D386F26A5CD6583\u0026rd\u003d1\u0026h\u003dquBMwmKlGwqC5wAU0K7n416plhWcR8zQCi7r-Fw9Y0w\u0026v\u003d1\u0026r\u003dhttps%3a%2f%2fwww.bing.com%2fsearch%3fq%3dsailflow%26FORM%3dUSBAPI\u0026p\u003dDevEx,5008.1",
          "displayText": "sailflow",
          "query": "sailflow",
          "searchKind": "WebSearch"
        },
        {
          "url": "https://www.bing.com/cr?IG\u003d2ACC4FE8B02F4AACB9182A6502B0E556\u0026CID\u003d1D546424A4CB64AF2D386F26A5CD6583\u0026rd\u003d1\u0026h\u003d0udadFl0gCTKCp0QmzQTXS3_y08iO8FpwsoKPHPS6kw\u0026v\u003d1\u0026r\u003dhttps%3a%2f%2fwww.bing.com%2fsearch%3fq%3dsailboatdata%26FORM%3dUSBAPI\u0026p\u003dDevEx,5009.1",
          "displayText": "sailboatdata",
          "query": "sailboatdata",
          "searchKind": "WebSearch"
        },
        {
          "url": "https://www.bing.com/cr?IG\u003d2ACC4FE8B02F4AACB9182A6502B0E556\u0026CID\u003d1D546424A4CB64AF2D386F26A5CD6583\u0026rd\u003d1\u0026h\u003deSSt0MRSbl2V0RFPSuVd-gC7fGOT4717pz55EBUgPec\u0026v\u003d1\u0026r\u003dhttps%3a%2f%2fwww.bing.com%2fsearch%3fq%3dsailor%2b2025%26FORM%3dUSBAPI\u0026p\u003dDevEx,5010.1",
          "displayText": "sailor 2025",
          "query": "sailor 2025",
          "searchKind": "WebSearch"
        }
      ]
    }
  ]
}
```

## <a name="next-steps"></a>Passos seguintes

> [!div class="nextstepaction"]
> [Criar uma aplicação Web de página única](../tutorials/autosuggest.md)

- [O que é a Sugestão Automática do Bing?](../get-suggested-search-terms.md)
- [Referência da API de Sugestão Automática do Bing v7](https://docs.microsoft.com/rest/api/cognitiveservices-bingsearch/bing-autosuggest-api-v7-reference)
