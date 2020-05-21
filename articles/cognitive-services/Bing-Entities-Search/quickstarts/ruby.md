---
title: 'Quickstart: Envie um pedido de pesquisa para a REST API usando Ruby - Bing Entity Search'
titleSuffix: Azure Cognitive Services
description: Utilize este quickstart para enviar um pedido para a API de Pesquisa de Entidades Bing usando ruby, e receber uma resposta JSON.
services: cognitive-services
author: aahill
manager: nitinme
ms.service: cognitive-services
ms.subservice: bing-entity-search
ms.topic: quickstart
ms.date: 05/08/2020
ms.author: aahi
ms.openlocfilehash: 9699bb0e75aac19a2d5c5c68d07e85b1c17c7cbc
ms.sourcegitcommit: fdec8e8bdbddcce5b7a0c4ffc6842154220c8b90
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 05/19/2020
ms.locfileid: "83650181"
---
# <a name="quickstart-send-a-search-request-to-the-bing-entity-search-rest-api-using-ruby"></a>Quickstart: Envie um pedido de pesquisa para a API de pesquisa da entidade bing usando ruby

Use este quickstart para fazer a sua primeira chamada para a API de Pesquisa de Entidades Bing e veja a resposta JSON. Esta simples aplicação Ruby envia uma consulta de pesquisa de notícias para a API, e exibe a resposta. O código fonte desta aplicação está disponível no [GitHub](https://github.com/Azure-Samples/cognitive-services-REST-api-samples/blob/master/ruby/Search/BingEntitySearchv7.rb).

Embora esta aplicação esteja escrita em Ruby, a API é um serviço Web RESTful compatível com a maioria dos idiomas de programação.

## <a name="prerequisites"></a>Pré-requisitos

* [Ruby 2.4](https://www.ruby-lang.org/en/downloads/) ou mais tarde.

[!INCLUDE [cognitive-services-bing-news-search-signup-requirements](../../../../includes/cognitive-services-bing-entity-search-signup-requirements.md)]

## <a name="create-and-initialize-the-application"></a>Criar e inicializar a aplicação

1. No seu IDE favorito ou editor de código, crie uma notícia do ficheiro Ruby e importe os seguintes pacotes:

    ```ruby
    require 'net/https'
    require 'cgi'
    require 'json'
    ```

2. Crie variáveis para o seu ponto final da API, URL de pesquisa de notícias, a sua chave de subscrição e consulta de pesquisa. Pode utilizar o ponto final global no seguinte código ou utilizar o ponto final de [subdomínio personalizado](../../../cognitive-services/cognitive-services-custom-subdomains.md) exibido no portal Azure para o seu recurso.
    
    ```ruby
    host = 'https://api.cognitive.microsoft.com'
    path = '/bing/v7.0/entities'
    
    mkt = 'en-US'
    query = 'italian restaurants near me'
    ```

## <a name="format-and-make-an-api-request"></a>Formatar e fazer um pedido de API

1. Crie a cadeia de parâmetros para o seu pedido, alinhando a sua variável de mercado ao `?mkt=` parâmetro. Codifique a sua consulta e adere-a ao `&q=` parâmetro. Combine o seu anfitrião, caminho e os parâmetros para o seu pedido e lance-os como um objeto URI.

    ```ruby
    params = '?mkt=' + mkt + '&q=' + CGI.escape(query)
    uri = URI (host + path + params)
    ```

2. Use as variáveis do último passo para criar o pedido. Adicione a sua chave de subscrição ao `Ocp-Apim-Subscription-Key` cabeçalho.

    ```ruby
    request = Net::HTTP::Get.new(uri)
    request['Ocp-Apim-Subscription-Key'] = subscriptionKey
    ```

3. Envie o pedido e imprima a resposta.

    ```ruby
    response = Net::HTTP.start(uri.host, uri.port, :use_ssl => uri.scheme == 'https') do |http|
        http.request (request)
    end

    puts JSON::pretty_generate (JSON (response.body))
    ```

## <a name="example-json-response"></a>Exemplo resposta JSON

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
        "url": "https://www.princi.com/",
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

## <a name="next-steps"></a>Próximos passos

> [!div class="nextstepaction"]
> [Criar uma aplicação web de página única](../tutorial-bing-entities-search-single-page-app.md)

* [O que é a API de Pesquisa de Entidades Bing?](../search-the-web.md)
* [Referência a API](https://docs.microsoft.com/rest/api/cognitiveservices-bingsearch/bing-entities-api-v7-reference)de Pesquisa de Entidades Bing .
