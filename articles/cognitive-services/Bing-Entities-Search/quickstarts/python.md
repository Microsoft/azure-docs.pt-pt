---
title: 'Quickstart: Envie um pedido de pesquisa para o REST API usando Python - Bing Entity Search'
titleSuffix: Azure Cognitive Services
description: Utilize este quickstart para enviar um pedido para a API de Pesquisa de Entidades Bing usando Python, e receber uma resposta JSON.
services: cognitive-services
author: aahill
manager: nitinme
ms.service: cognitive-services
ms.subservice: bing-entity-search
ms.topic: quickstart
ms.date: 12/11/2019
ms.author: aahi
ms.openlocfilehash: 07b563308e80055d699d1cefeb3b2db71ffa4cd7
ms.sourcegitcommit: 34a6fa5fc66b1cfdfbf8178ef5cdb151c97c721c
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/28/2020
ms.locfileid: "75448619"
---
# <a name="quickstart-send-a-search-request-to-the-bing-entity-search-rest-api-using-python"></a>Quickstart: Envie um pedido de pesquisa para a API de pesquisa da entidade bing usando Python

Use este quickstart para fazer a sua primeira chamada para a API de Pesquisa de Entidades Bing e veja a resposta JSON. Esta simples aplicação Python envia uma consulta de pesquisa de notícias para a API, e exibe a resposta. O código fonte desta amostra está disponível no [GitHub](https://github.com/Azure-Samples/cognitive-services-REST-api-samples/blob/master/python/Search/BingEntitySearchv7.py).

Embora esta aplicação esteja escrita em Python, a API é um serviço Web RESTful compatível com a maioria das linguagens de programação.

## <a name="prerequisites"></a>Pré-requisitos

* [Pitão](https://www.python.org/downloads/) 2.x ou 3.x

[!INCLUDE [cognitive-services-bing-news-search-signup-requirements](../../../../includes/cognitive-services-bing-entity-search-signup-requirements.md)]

## <a name="create-and-initialize-the-application"></a>Criar e inicializar a aplicação

1. Crie um novo ficheiro Python no seu IDE ou editor favorito e adicione as seguintes importações. Crie variáveis para a sua chave de subscrição, ponto final, mercado e uma consulta de pesquisa. Pode utilizar o ponto final global abaixo, ou o ponto final personalizado do [subdomínio](../../../cognitive-services/cognitive-services-custom-subdomains.md) exibido no portal Azure para o seu recurso.

    ```python
    import http.client, urllib.parse
    import json
    
    subscriptionKey = 'ENTER YOUR KEY HERE'
    host = 'api.cognitive.microsoft.com'
    path = '/bing/v7.0/entities'
    mkt = 'en-US'
    query = 'italian restaurants near me'
    ```

2. Crie um url de pedido, `?mkt=` afuniando a sua variável de mercado ao parâmetro. Codificar a sua consulta e acessá-la ao `&q=` parâmetro. 
    
    ```python
    params = '?mkt=' + mkt + '&q=' + urllib.parse.quote (query)
    ```

## <a name="send-a-request-and-get-a-response"></a>Envie um pedido e obtenha uma resposta

1. Criar uma `get_suggestions()`função chamada . Em seguida, execute os seguintes passos.
   1. Adicione a sua chave de `Ocp-Apim-Subscription-Key` subscrição a um dicionário com como chave.
   2. Utilize `http.client.HTTPSConnection()` para criar um objeto de cliente HTTPS. Envie `GET` um `request()` pedido utilizando o seu caminho e parâmetros e informações sobre cabeçalho.
   3. Guarde a `getresponse()`resposta `response.read()`com e devolva.

      ```python
      def get_suggestions ():
       headers = {'Ocp-Apim-Subscription-Key': subscriptionKey}
       conn = http.client.HTTPSConnection (host)
       conn.request ("GET", path + params, None, headers)
       response = conn.getresponse ()
       return response.read()
      ```

2. Ligue `get_suggestions()`e imprima a resposta da JSON.

    ```python
    result = get_suggestions ()
    print (json.dumps(json.loads(result), indent=4))
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

## <a name="next-steps"></a>Passos seguintes

> [!div class="nextstepaction"]
> [Criar uma aplicação web de página única](../tutorial-bing-entities-search-single-page-app.md)

* [O que é a API de Pesquisa de Entidadebing](../search-the-web.md)
* [Referência da API da Entidade Bing](https://docs.microsoft.com/rest/api/cognitiveservices-bingsearch/bing-entities-api-v7-reference)
