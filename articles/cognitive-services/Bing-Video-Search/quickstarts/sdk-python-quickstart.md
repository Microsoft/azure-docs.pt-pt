---
title: 'Quickstart: Pesquisa de vídeos usando o SDK para Python - Bing Video Search'
titleSuffix: Azure Cognitive Services
description: Use este quickstart para enviar pedidos de pesquisa de vídeo usando o Bing Video Search SDK para Python
services: cognitive-services
author: aahill
manager: nitinme
ms.service: cognitive-services
ms.subservice: bing-video-search
ms.topic: quickstart
ms.date: 02/11/2020
ms.author: aahi
ms.openlocfilehash: 9d7b2a8950134e530e042e862a1c19abe89fd78d
ms.sourcegitcommit: 333af18fa9e4c2b376fa9aeb8f7941f1b331c11d
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 02/13/2020
ms.locfileid: "77201225"
---
# <a name="quickstart-perform-a-video-search-with-the-bing-video-search-sdk-for-python"></a>Quickstart: Realize uma pesquisa de vídeo com o Bing Video Search SDK para Python

Use este quickstart para começar a procurar notícias com o Bing Video Search SDK para Python. Embora o Bing Video Search tenha um Rest API compatível com a maioria dos idiomas de programação, o SDK fornece uma forma fácil de integrar o serviço nas suas aplicações. O código fonte desta amostra pode ser encontrado no [GitHub](https://github.com/Azure-Samples/cognitive-services-python-sdk-samples/blob/master/samples/search/video_search_samples.py) com anotações adicionais e funcionalidades.

[!INCLUDE [cognitive-services-bing-video-search-signup-requirements](../../../../includes/cognitive-services-bing-video-search-signup-requirements.md)]

## <a name="prerequisites"></a>Pré-requisitos

- [Pitão](https://www.python.org/) 2.x ou 3.x
- O Bing Video Search SDK para python

Recomenda-se que utilize um [ambiente virtual](https://docs.python.org/3/tutorial/venv.html)python. Pode instalar e inicializar um ambiente virtual com o [módulo venv](https://pypi.python.org/pypi/virtualenv). Instale o virtualenv para Python 2.7 com:

```console
python -m venv mytestenv
```

Instale o Bing Video Search SDK com:

```console
cd mytestenv
python -m pip install azure-cognitiveservices-search-videosearch
```

## <a name="create-and-initialize-the-application"></a>Criar e inicializar a aplicação

1. Crie um novo ficheiro Python no seu IDE ou editor favorito e adicione as seguintes declarações de importação. 

    ```python
    from azure.cognitiveservices.search.videosearch import VideoSearchClient
    from azure.cognitiveservices.search.videosearch.models import VideoPricing, VideoLength, VideoResolution, VideoInsightModule
    from msrest.authentication import CognitiveServicesCredentials
    ```

2. Crie uma variável para a sua chave de subscrição. 

    ```python
    subscription_key = "YOUR-SUBSCRIPTION-KEY"
    endpoint = "YOUR-ENDPOINT"
    ```

## <a name="create-the-search-client"></a>Criar o cliente de pesquisa

Crie uma instância do `CognitiveServicesCredentials`e instancie o cliente:

```python
client = VideoSearchAPI(endpoint, CognitiveServicesCredentials(subscription_key))
```

## <a name="send-a-search-request-and-get-a-response"></a>Envie um pedido de pesquisa e obtenha uma resposta

1. Use `client.videos.search()` com a sua consulta de pesquisa para enviar um pedido para a API de Pesquisa de Vídeo Bing, e obter uma resposta.

    ```python
    video_result = client.videos.search(query="SwiftKey")
    ```

2. Se a resposta contiver resultados de pesquisa, obtenha o primeiro e imprima o seu ID, nome e url.

    ```python
    if video_result.value:
        first_video_result = video_result.value[0]
        print("Video result count: {}".format(len(video_result.value)))
        print("First video id: {}".format(first_video_result.video_id))
        print("First video name: {}".format(first_video_result.name))
        print("First video url: {}".format(first_video_result.content_url))
    else:
        print("Didn't see any video result data..")
    ```

## <a name="next-steps"></a>Passos seguintes

> [!div class="nextstepaction"]
> [Criar uma única página web app](../tutorial-bing-video-search-single-page-app.md)

## <a name="see-also"></a>Veja também 

- [O que é a API de Pesquisa de Vídeo Bing?](../overview.md)
- [Exemplos de SDK .NET nos serviços cognitivos](https://github.com/Azure-Samples/cognitive-services-dotnet-sdk-samples/tree/master/BingSearchv7)
