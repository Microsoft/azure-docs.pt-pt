---
title: 'Início rápido: Enviar um pedido de pesquisa para a API de REST do Search do Bing entidade com Ruby'
titlesuffix: Azure Cognitive Services
description: Utilize este guia de introdução para enviar um pedido para a API de REST do Search do Bing entidade com Ruby e receber uma resposta JSON.
services: cognitive-services
author: aahill
manager: nitinme
ms.service: cognitive-services
ms.subservice: bing-entity-search
ms.topic: quickstart
ms.date: 02/01/2019
ms.author: aahi
ms.openlocfilehash: a2bf7d018712d311545422b91eac0c6ca4912960
ms.sourcegitcommit: 7e772d8802f1bc9b5eb20860ae2df96d31908a32
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/06/2019
ms.locfileid: "57439383"
---
# <a name="quickstart-for-bing-entity-search-api-with-ruby"></a>Início Rápido da API de Pesquisa de Entidades do Bing com Ruby

Utilize este guia de introdução para efetuar a primeira chamada para a API de pesquisa de entidades do Bing e ver a resposta JSON. Esta aplicação Ruby simple envia uma consulta de pesquisa de notícias para a API e apresenta a resposta. O código-fonte para esta aplicação está disponível no [GitHub](https://github.com/Azure-Samples/cognitive-services-REST-api-samples/blob/master/ruby/Search/BingEntitySearchv7.rb).

Embora esta aplicação seja escrita em Ruby, a API é um serviço Web RESTful compatível com a maioria das linguagens de programação.

## <a name="prerequisites"></a>Pré-requisitos

* [Ruby 2.4](https://www.ruby-lang.org/en/downloads/) ou posterior.

[!INCLUDE [cognitive-services-bing-news-search-signup-requirements](../../../../includes/cognitive-services-bing-entity-search-signup-requirements.md)]

## <a name="create-and-initialize-the-application"></a>Criar e inicializar a aplicação

1. No seu IDE favorito ou editor de código, crie uma ficheiro Ruby de notícias e importar os seguintes pacotes.

    ```ruby
    require 'net/https'
    require 'cgi'
    require 'json'
    ```

2. Crie variáveis para o ponto final de API, o URL de pesquisa de notícias, a chave de subscrição e uma consulta de pesquisa.
    
    ```ruby
    host = 'https://api.cognitive.microsoft.com'
    path = '/bing/v7.0/entities'
    
    mkt = 'en-US'
    query = 'italian restaurants near me'
    ```

## <a name="format-and-make-an-api-request"></a>Formatar e fazer um pedido de API

1. Criar a cadeia de parâmetros para o seu pedido, acrescentando a variável de mercado para o `?mkt=` parâmetro. Codificar a sua consulta e acrescentá-lo para o `&q=` parâmetro. Combine o seu anfitrião de API, caminho e os parâmetros para o seu pedido e convertê-los como um objeto URI.

    ```ruby
    params = '?mkt=' + mkt + '&q=' + CGI.escape(query)
    uri = URI (host + path + params)
    ```

2. Utilize as variáveis do último passo para criar o pedido. Adicionar a chave de subscrição para o `Ocp-Apim-Subscription-Key` cabeçalho.

    ```ruby
    request = Net::HTTP::Get.new(uri)
    request['Ocp-Apim-Subscription-Key'] = subscriptionKey
    ```

3. Enviar a solicitação e resposta de impressão

    ```ruby
    response = Net::HTTP.start(uri.host, uri.port, :use_ssl => uri.scheme == 'https') do |http|
        http.request (request)
    end

    puts JSON::pretty_generate (JSON (response.body))
    ```

## <a name="example-json-response"></a>Resposta JSON de exemplo

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
        "url": "http://www.princi.com/",
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

## <a name="next-steps"></a>Passos Seguintes

> [!div class="nextstepaction"]
> [Criar uma aplicação web de página única](../tutorial-bing-entities-search-single-page-app.md)

* [O que é a API de pesquisa de entidades do Bing](../search-the-web.md)
* [Referência de API de pesquisa de entidades do Bing](https://docs.microsoft.com/rest/api/cognitiveservices/bing-entities-api-v7-reference)
