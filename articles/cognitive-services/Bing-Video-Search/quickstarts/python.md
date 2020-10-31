---
title: 'Quickstart: Procure vídeos usando a API REST e Python - Bing Video Search'
titleSuffix: Azure Cognitive Services
description: Utilize este quickstart para enviar pedidos de pesquisa de vídeo para a API Bing Video Search REST usando Python.
services: cognitive-services
author: aahill
manager: nitinme
ms.service: cognitive-services
ms.subservice: bing-video-search
ms.topic: quickstart
ms.date: 05/22/2020
ms.author: aahi
ms.custom: devx-track-python
ms.openlocfilehash: de29b4bfc15fdec30acd043564e99ebe6e02454d
ms.sourcegitcommit: 3bdeb546890a740384a8ef383cf915e84bd7e91e
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/30/2020
ms.locfileid: "93094086"
---
# <a name="quickstart-search-for-videos-using-the-bing-video-search-rest-api-and-python"></a>Quickstart: Procure vídeos utilizando a API e a Python de pesquisa de vídeo Bing

> [!WARNING]
> As APIs de Pesquisa de Bing estão a mover-se dos Serviços Cognitivos para os Serviços de Pesquisa Bing. A partir **de 30 de outubro de 2020,** quaisquer novos casos de Bing Search devem ser adquir-se na sequência do processo [aqui](https://aka.ms/cogsvcs/bingmove)documentado.
> Bing Search APIs aforados usando Serviços Cognitivos será suportado durante os próximos três anos ou até o final do seu Contrato de Empresa, o que acontecer primeiro.
> Para obter instruções de migração, consulte [os Serviços de Busca Bing.](https://aka.ms/cogsvcs/bingmigration)

Use este quickstart para fazer a sua primeira chamada para a API de Pesquisa de Vídeo Bing. Esta simples aplicação Python envia uma consulta de pesquisa de vídeo HTTP para a API e exibe a resposta JSON. Embora esta aplicação esteja escrita em Python, a API é um serviço Web RESTful compatível com a maioria das linguagens de programação. 

O código fonte deste exemplo está disponível [no GitHub](https://github.com/Azure-Samples/cognitive-services-REST-api-samples/blob/master/python/Search/BingVideoSearchv7.py) com processamento de erros e anotações de código adicionais.

Pode executar este exemplo como um portátil Jupyter no [MyBinder](https://mybinder.org) selecionando o crachá de encaixe de **lançamento:** 

[![Encadernação](https://mybinder.org/badge.svg)](https://mybinder.org/v2/gh/Microsoft/cognitive-services-notebooks/master?filepath=BingVideoSearchAPI.ipynb)


## <a name="prerequisites"></a>Pré-requisitos

* Python [2.x ou 3.x](https://python.org)

[!INCLUDE [cognitive-services-bing-video-search-signup-requirements](../../../../includes/cognitive-services-bing-video-search-signup-requirements.md)]

## <a name="initialize-the-application"></a>Inicializar a aplicação

1. Crie um novo ficheiro Python no seu IDE ou editor favorito e importe as seguintes bibliotecas:

    ```python
    import requests
    from IPython.display import HTML
    ```
2.  Crie variáveis para a sua chave de subscrição, ponto final de pesquisa e termo de pesquisa. Pelo `search_url` valor, pode utilizar o ponto final global no seguinte código ou utilizar o ponto final [de subdomínio personalizado](../../../cognitive-services/cognitive-services-custom-subdomains.md) apresentado no portal Azure para o seu recurso.
    
    ```python
    subscription_key = None
    assert subscription_key
    search_url = "https://api.cognitive.microsoft.com/bing/v7.0/videos/search"
    search_term = "kittens"
    ```

3. Adicione a chave de subscrição a um `Ocp-Apim-Subscription-Key` cabeçalho criando um novo dicionário para associar a corda do cabeçalho à sua chave.

    ```python
    headers = {"Ocp-Apim-Subscription-Key" : subscription_key}
    ```

## <a name="send-your-request"></a>Envie o seu pedido

1. Adicione os parâmetros ao seu pedido criando um dicionário denominado `params` . Adicione os seus termos de pesquisa ao `q` parâmetro: uma contagem de vídeo de 5, `free` para o preço dos vídeos devolvidos, e para o comprimento do `short` vídeo.

    ```python
    params  = {"q": search_term, "count":5, "pricing": "free", "videoLength":"short"}
    ```

2. Use a `requests` biblioteca em Python para ligar para a API de pesquisa de vídeo Bing. Passe a chave API e os parâmetros de pesquisa utilizando o `headers` dicionário `params` e dicionário.
    
    ```python
    response = requests.get(search_url, headers=headers, params=params)
    response.raise_for_status()
    search_results = response.json()
    ```

3. Para ver um dos vídeos devolvidos, obtenha um resultado de pesquisa do `search_results` objeto. Insira a propriedade do resultado `embedHtml` num `IFrame` .  
    
    ```python
    HTML(search_results["value"][0]["embedHtml"].replace("autoplay=1","autoplay=0"))
    ```


## <a name="json-response"></a>Resposta JSON

É devolvida uma resposta com êxito em JSON, tal como é apresentado no exemplo seguinte: 

```json
{
    "_type": "Videos",
    "instrumentation": {},
    "readLink": "https://api.cognitive.microsoft.com/api/v7/videos/search?q=kittens",
    "webSearchUrl": "https://www.bing.com/videos/search?q=kittens",
    "totalEstimatedMatches": 1000,
    "value": [
        {
            "webSearchUrl": "https://www.bing.com/videos/search?q=kittens&view=...",
            "name": "Top 10 cute kitten videos compilation",
            "description": "HELP HOMELESS ANIMALS AND WIN A PRIZE BY CHOOSING...",
            "thumbnailUrl": "https://tse4.mm.bing.net/th?id=OVP.n1aE_Oikl4MtzBb...",
            "datePublished": "2014-11-12T22:47:36.0000000",
            "publisher": [
                {
                    "name": "Fabrikam"
                }
            ],
            "creator": {
                "name": "Marcus Appel"
            },
            "isAccessibleForFree": true,
            "contentUrl": "https://www.fabrikam.com/watch?v=8HVWitAW-Qg",
            "hostPageUrl": "https://www.fabrikam.com/watch?v=8HVWitAW-Qg",
            "encodingFormat": "h264",
            "hostPageDisplayUrl": "https://www.fabrikam.com/watch?v=8HVWitAW-Qg",
            "width": 480,
            "height": 360,
            "duration": "PT3M52S",
            "motionThumbnailUrl": "https://tse4.mm.bing.net/th?id=OM.j4QyJAENJphdZQ_1501386166&pid=Api",
            "embedHtml": "<iframe width=\"1280\" height=\"720\" src=\"https://www.fabrikam.com/embed/8HVWitAW-Qg?autoplay=1\" frameborder=\"0\" allowfullscreen></iframe>",
            "allowHttpsEmbed": true,
            "viewCount": 7513633,
            "thumbnail": {
                "width": 300,
                "height": 168
            },
            "videoId": "655D98260D012432848F6558260D012432848F",
            "allowMobileEmbed": true,
            "isSuperfresh": false
        },
        . . .
    ],
    "nextOffset": 36,
    "queryExpansions": [
        {
            "text": "Kittens Meowing",
            "displayText": "Meowing",
            "webSearchUrl": "https://www.bing.com/videos/search?q=Kittens+Meowing...",
            "searchLink": "https://api.cognitive.microsoft.com/api/v7/videos/search...",
            "thumbnail": {
                "thumbnailUrl": "https://tse3.mm.bing.net/th?q=Kittens+Meowing&pid..."
            }
        },
        {
            "text": "Funny Kittens",
            "displayText": "Funny",
            "webSearchUrl": "https://www.bing.com/videos/search?q=Funny+Kittens...",
            "searchLink": "https://api.cognitive.microsoft.com/api/v7/videos/search...",
            "thumbnail": {
                "thumbnailUrl": "https://tse3.mm.bing.net/th?q=Funny+Kittens&..."
            }
        },
        . . .
    ],
    "pivotSuggestions": [
        {
            "pivot": "kittens",
            "suggestions": [
                {
                    "text": "Cat",
                    "displayText": "Cat",
                    "webSearchUrl": "https://www.bing.com/videos/search?q=Cat...",
                    "searchLink": "https://api.cognitive.microsoft.com/api/v7/videos/search?...",
                    "thumbnail": {
                        "thumbnailUrl": "https://tse3.mm.bing.net/th?q=Cat&pid=Api..."
                    }
                },
                {
                    "text": "Feral Cat",
                    "displayText": "Feral Cat",
                    "webSearchUrl": "https://www.bing.com/videos/search?q=Feral+Cat...",
                    "searchLink": "https://api.cognitive.microsoft.com/api/v7/videos/search...",
                    "thumbnail": {
                        "thumbnailUrl": "https://tse3.mm.bing.net/th?q=Feral+Cat&pid=Api&..."
                    }
                }
            ]
        }
    ],
    "relatedSearches": [
        {
            "text": "Kittens Being Born",
            "displayText": "Kittens Being Born",
            "webSearchUrl": "https://www.bing.com/videos/search?q=Kittens+Being+Born...",
            "searchLink": "https://api.cognitive.microsoft.com/api/v7/videos/search?...",
            "thumbnail": {
                "thumbnailUrl": "https://tse1.mm.bing.net/th?q=Kittens+Being+Born&pid=..."
            }
        },
        . . .
    ]
}
```

## <a name="next-steps"></a>Passos seguintes

> [!div class="nextstepaction"]
> [Criar uma aplicação web de página única](../tutorial-bing-video-search-single-page-app.md)

## <a name="see-also"></a>Ver também 

 [O que é a API de Pesquisa de Vídeos do Bing?](../overview.md)
