---
title: 'Quickstart: Verifique a ortografia com a REST API e Ruby - Bing Spell Check'
titleSuffix: Azure Cognitive Services
description: Comece a usar a API bing spell check REST para verificar a ortografia e a gramática com este arranque rápido.
services: cognitive-services
author: aahill
manager: nitinme
ms.service: cognitive-services
ms.subservice: bing-spell-check
ms.topic: quickstart
ms.date: 05/21/2020
ms.author: aahi
ms.openlocfilehash: 9f5f841bd7fd33d4d6c7dcd1a1f7ab754610b973
ms.sourcegitcommit: 64fc70f6c145e14d605db0c2a0f407b72401f5eb
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 05/27/2020
ms.locfileid: "83869898"
---
# <a name="quickstart-check-spelling-with-the-bing-spell-check-rest-api-and-ruby"></a>Quickstart: Verifique a ortografia com o Bing Spell Check REST API e Ruby

Use este quickstart para fazer a sua primeira chamada para a API de Bing Spell Check REST usando Ruby. Este simples pedido envia um pedido à API e devolve uma lista de correções sugeridas. 

Embora esta aplicação esteja escrita em Ruby, a API é um serviço Web RESTful compatível com a maioria dos idiomas de programação. O código fonte para esta aplicação está disponível no [GitHub](https://github.com/Azure-Samples/cognitive-services-REST-api-samples/blob/master/ruby/Search/BingSpellCheckv7.rb)

## <a name="prerequisites"></a>Pré-requisitos

* [Ruby 2.4](https://www.ruby-lang.org/en/downloads/) ou mais tarde.

[!INCLUDE [cognitive-services-bing-spell-check-signup-requirements](../../../../includes/cognitive-services-bing-spell-check-signup-requirements.md)]


## <a name="create-and-initialize-the-application"></a>Criar e inicializar a aplicação

1. Crie um novo ficheiro Ruby no seu editor favorito ou IDE, e adicione os seguintes requisitos: 

    ```ruby
    require 'net/http'
    require 'uri'
    require 'json'
    ```

2. Crie variáveis para a sua chave de subscrição, ponto final URI e caminho. Pode utilizar o ponto final global no seguinte código ou utilizar o ponto final de [subdomínio personalizado](../../../cognitive-services/cognitive-services-custom-subdomains.md) exibido no portal Azure para o seu recurso. Crie os parâmetros do seu pedido:

   a. Atribua o seu código de mercado ao `mkt` parâmetro com o `=` operador. O código de mercado é o código do país/região a quem faz o pedido. 

   b. Adicione o `mode` parâmetro com o operador `&` e, em seguida, atribua o modo de verificação ortográfica. O modo pode ser `proof` (captura a maioria dos erros ortográficos/gramaticais) ou (apanha a maioria dos `spell` erros ortográficos, mas não tantos erros gramaticais). 

    ```ruby
    key = 'ENTER YOUR KEY HERE'
    uri = 'https://api.cognitive.microsoft.com'
    path = '/bing/v7.0/spellcheck?'
    params = 'mkt=en-us&mode=proof'
    ```

## <a name="send-a-spell-check-request"></a>Envie um pedido de verificação de feitiços

1. Crie um URI a partir da corda uri, caminho e parâmetros do seu hospedeiro. Detete a sua consulta para conter o texto que pretende soletrar.

   ```ruby
   uri = URI(uri + path + params)
   uri.query = URI.encode_www_form({
      # Request parameters
   'text' => 'Hollo, wrld!'
   })
   ```

2. Crie um pedido utilizando o URI construído anteriormente. Adicione a chave ao `Ocp-Apim-Subscription-Key` cabeçalho.

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

4. Obtenha a resposta JSON e imprima-a para a consola. 

    ```ruby
    result = JSON.pretty_generate(JSON.parse(response.body))
    puts result
    ```

## <a name="run-the-application"></a>Executar a aplicação

Construa e gereno seu projeto. Se estiver a utilizar a linha de comando, utilize o seguinte comando para executar a aplicação:

   ```bash
   ruby <FILE_NAME>.rb
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
> [Criar uma aplicação web de página única](../tutorials/spellcheck.md)

- [O que é a API de Verificação Ortográfica do Bing?](../overview.md)
- [Referência de Bing Spell Check API v7](https://docs.microsoft.com/rest/api/cognitiveservices-bingsearch/bing-spell-check-api-v7-reference)
