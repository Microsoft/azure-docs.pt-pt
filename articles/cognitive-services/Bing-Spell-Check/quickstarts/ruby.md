---
title: 'Início rápido: Verificar a ortografia com a API REST do Verificação Ortográfica do Bing e o Ruby'
titleSuffix: Azure Cognitive Services
description: Comece a usar a API REST do Verificação Ortográfica do Bing para verificar a ortografia e a gramática.
services: cognitive-services
author: aahill
manager: nitinme
ms.service: cognitive-services
ms.subservice: bing-spell-check
ms.topic: quickstart
ms.date: 09/13/2019
ms.author: aahi
ms.openlocfilehash: bf038b97335db20349577f754bfa41e1b98ee9b7
ms.sourcegitcommit: 1752581945226a748b3c7141bffeb1c0616ad720
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 09/14/2019
ms.locfileid: "70996736"
---
# <a name="quickstart-check-spelling-with-the-bing-spell-check-rest-api-and-ruby"></a>Início rápido: Verificar a ortografia com a API REST do Verificação Ortográfica do Bing e o Ruby

Use este guia de início rápido para fazer sua primeira chamada para a API REST do Verificação Ortográfica do Bing usando Ruby. Esse aplicativo simples envia uma solicitação para a API e retorna uma lista de palavras que ela não reconheceu, seguida de correções sugeridas. Embora esta aplicação seja escrita em Ruby, a API é um serviço Web RESTful compatível com a maioria das linguagens de programação. O código-fonte para este aplicativo está disponível no [GitHub](https://github.com/Azure-Samples/cognitive-services-REST-api-samples/blob/master/ruby/Search/BingSpellCheckv7.rb)

## <a name="prerequisites"></a>Pré-requisitos

* [Ruby 2,4](https://www.ruby-lang.org/en/downloads/) ou posterior.

[!INCLUDE [cognitive-services-bing-spell-check-signup-requirements](../../../../includes/cognitive-services-bing-spell-check-signup-requirements.md)]


## <a name="create-and-initialize-the-application"></a>Criar e inicializar a aplicação

1. Crie um novo arquivo Ruby em seu editor favorito ou IDE e adicione os requisitos a seguir. 

    ```javascript
    require 'net/http'
    require 'uri'
    require 'json'
    ```

2. Crie variáveis para sua chave de assinatura, URI de ponto de extremidade e caminho. Crie seus parâmetros de solicitação acrescentando o `mkt=` parâmetro ao mercado e `&mode` ao modo de prova `proof` de prelo.

    ```ruby
    key = 'ENTER YOUR KEY HERE'
    uri = 'https://api.cognitive.microsoft.com'
    path = '/bing/v7.0/spellcheck?'
    params = 'mkt=en-us&mode=proof'
    ```

## <a name="send-a-spell-check-request"></a>Enviar uma solicitação de verificação ortográfica

1. Crie um URI do URI do host, do caminho e da cadeia de parâmetros. Defina sua consulta para conter o texto que você deseja verificar na verificação ortográfica.

   ```ruby
   uri = URI(uri + path + params)
   uri.query = URI.encode_www_form({
      # Request parameters
   'text' => 'Hollo, wrld!'
   })
   ```

2. Crie uma solicitação usando o URI construído acima. Adicione sua chave ao `Ocp-Apim-Subscription-Key` cabeçalho.

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

4. Obtenha a resposta JSON e imprima-a no console do. 

    ```ruby
    result = JSON.pretty_generate(JSON.parse(response.body))
    puts result
    ```

## <a name="example-json-response"></a>Exemplo de resposta JSON

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
> [Criar um aplicativo Web de página única](../tutorials/spellcheck.md)

- [O que é a API de Verificação Ortográfica do Bing?](../overview.md)
- [Bing Spell Check API v7 Reference](https://docs.microsoft.com/rest/api/cognitiveservices-bingsearch/bing-spell-check-api-v7-reference) (Referência da API de Verificação de Ortografia do Bing v7)
