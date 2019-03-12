---
title: 'Início rápido: Enviar um pedido de pesquisa para a API de REST do Search do Bing entidade com o Python'
titlesuffix: Azure Cognitive Services
description: Utilize este guia de introdução para enviar um pedido para a API de REST do Search do Bing entidade com o Python e receber uma resposta JSON.
services: cognitive-services
author: aahill
manager: nitinme
ms.service: cognitive-services
ms.subservice: bing-entity-search
ms.topic: quickstart
ms.date: 02/01/2019
ms.author: aahi
ms.openlocfilehash: 14bd9a0bfbef15acdac5a2eddc88c72d3e142591
ms.sourcegitcommit: bd15a37170e57b651c54d8b194e5a99b5bcfb58f
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/07/2019
ms.locfileid: "57537971"
---
# <a name="quickstart-send-a-search-request-to-the-bing-entity-search-rest-api-using-python"></a>Início rápido: Enviar um pedido de pesquisa para a API de REST do Search do Bing entidade com o Python

Utilize este guia de introdução para efetuar a primeira chamada para a API de pesquisa de entidades do Bing e ver a resposta JSON. Esta aplicação de Python simple envia uma consulta de pesquisa de notícias para a API e apresenta a resposta. O código fonte deste exemplo está disponível no [GitHub](https://github.com/Azure-Samples/cognitive-services-REST-api-samples/blob/master/python/Search/BingEntitySearchv7.py).

Embora esta aplicação esteja escrita em Python, a API é um serviço Web RESTful compatível com a maioria das linguagens de programação.

## <a name="prerequisites"></a>Pré-requisitos

* [Python](https://www.python.org/downloads/) 2.x ou 3.x

[!INCLUDE [cognitive-services-bing-news-search-signup-requirements](../../../../includes/cognitive-services-bing-entity-search-signup-requirements.md)]

## <a name="create-and-initialize-the-application"></a>Criar e inicializar a aplicação

1. Criar um novo ficheiro de Python no seu IDE ou editor favorito e adicione as seguintes importações. Crie variáveis para a chave de subscrição, o ponto final, o mercado e uma consulta de pesquisa. Pode encontrar o ponto final no dashboard do Azure.

    ```python
    import http.client, urllib.parse
    import json
    
    subscriptionKey = 'ENTER YOUR KEY HERE'
    host = 'api.cognitive.microsoft.com'
    path = '/bing/v7.0/entities'
    mkt = 'en-US'
    query = 'italian restaurants near me'
    ```

2. Criar um pedido de url, acrescentando a variável de mercado para o `?mkt=` parâmetro. Codificar o URL da sua consulta e anexando-o para o `&q=` parâmetro. 
    
    ```python
    params = '?mkt=' + mkt + '&q=' + urllib.parse.quote (query)
    ```

## <a name="send-a-request-and-get-a-response"></a>Enviar um pedido e obter uma resposta

1. Criar uma função chamada `get_suggestions()`. Em seguida, execute os seguintes passos.
    1. Adicionar a chave de subscrição a um dicionário com `Ocp-Apim-Subscription-Key` como uma chave.
    2. Utilize `http.client.HTTPSConnection()` para criar um objeto de cliente HTTPS. Enviar um `GET` solicite utilizando `request()` com as suas informações de cabeçalho e parâmetros e caminho.
    3. Store a resposta com `getresponse()`e retornar `response.read()`.

    ```python
    def get_suggestions ():
        headers = {'Ocp-Apim-Subscription-Key': subscriptionKey}
        conn = http.client.HTTPSConnection (host)
        conn.request ("GET", path + params, None, headers)
        response = conn.getresponse ()
        return response.read()
    ```

2. Chamar `get_suggestions()`e a resposta json de impressão.

    ```python
    result = get_suggestions ()
    print (json.dumps(json.loads(result), indent=4))
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

## <a name="next-steps"></a>Passos Seguintes

> [!div class="nextstepaction"]
> [Criar uma aplicação web de página única](../tutorial-bing-entities-search-single-page-app.md)

* [O que é a API de pesquisa de entidades do Bing](../search-the-web.md)
* [Referência de API de pesquisa de entidades do Bing](https://docs.microsoft.com/rest/api/cognitiveservices/bing-entities-api-v7-reference)
