---
title: 'Quickstart: Check spelling with the REST API and Ruby - Bing Spell Check'
titleSuffix: Azure Cognitive Services
description: Get started using the Bing Spell Check REST API to check spelling and grammar.
services: cognitive-services
author: aahill
manager: nitinme
ms.service: cognitive-services
ms.subservice: bing-spell-check
ms.topic: quickstart
ms.date: 09/13/2019
ms.author: aahi
ms.openlocfilehash: e80a7bd3b56ccfd13a20c11f845d076271448b76
ms.sourcegitcommit: f523c8a8557ade6c4db6be12d7a01e535ff32f32
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 11/22/2019
ms.locfileid: "74383846"
---
# <a name="quickstart-check-spelling-with-the-bing-spell-check-rest-api-and-ruby"></a>Quickstart: Check spelling with the Bing Spell Check REST API and Ruby

Use this quickstart to make your first call to the Bing Spell Check REST API using Ruby. This simple application sends a request to the API and returns a list of words it didn't recognize, followed by suggested corrections. Embora esta aplicação seja escrita em Ruby, a API é um serviço Web RESTful compatível com a maioria das linguagens de programação. The source code for this application is available on [GitHub](https://github.com/Azure-Samples/cognitive-services-REST-api-samples/blob/master/ruby/Search/BingSpellCheckv7.rb)

## <a name="prerequisites"></a>Pré-requisitos

* [Ruby 2.4](https://www.ruby-lang.org/en/downloads/) or later.

[!INCLUDE [cognitive-services-bing-spell-check-signup-requirements](../../../../includes/cognitive-services-bing-spell-check-signup-requirements.md)]


## <a name="create-and-initialize-the-application"></a>Criar e inicializar a aplicação

1. Create a new Ruby file in your favorite editor or IDE, and add the following requirements. 

    ```javascript
    require 'net/http'
    require 'uri'
    require 'json'
    ```

2. Create variables for your subscription key, endpoint URI and path. Create your request parameters by appending the `mkt=` parameter to your market, and `&mode` to the `proof` proof mode.

    ```ruby
    key = 'ENTER YOUR KEY HERE'
    uri = 'https://api.cognitive.microsoft.com'
    path = '/bing/v7.0/spellcheck?'
    params = 'mkt=en-us&mode=proof'
    ```

## <a name="send-a-spell-check-request"></a>Send a spell check request

1. Create a URI from your host uri, path, and parameters string. Set its query to contain the text you want to spell check.

   ```ruby
   uri = URI(uri + path + params)
   uri.query = URI.encode_www_form({
      # Request parameters
   'text' => 'Hollo, wrld!'
   })
   ```

2. Create a request using the URI constructed above. Add your key to the `Ocp-Apim-Subscription-Key` header.

    ```ruby
    request = Net::HTTP::Post.new(uri)
    request['Content-Type'] = "application/x-www-form-urlencoded"
    request['Ocp-Apim-Subscription-Key'] = key
    ```

3. Envie o pedido.

    ```ruby
    response = Net::HTTP.start(uri.host, uri.port, :use_ssl => uri.scheme == 'https') do |http|
        http.request(request)
    end
    ```

4. Get the JSON response, and print it to the console. 

    ```ruby
    result = JSON.pretty_generate(JSON.parse(response.body))
    puts result
    ```

## <a name="example-json-response"></a>Example JSON response

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
> [Create a single page web-app](../tutorials/spellcheck.md)

- [What is the Bing Spell Check API?](../overview.md)
- [Bing Spell Check API v7 Reference](https://docs.microsoft.com/rest/api/cognitiveservices-bingsearch/bing-spell-check-api-v7-reference) (Referência da API de Verificação de Ortografia do Bing v7)
