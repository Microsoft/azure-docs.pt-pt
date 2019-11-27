---
title: 'Início rápido: pesquisar vídeos usando o SDK para Python-Pesquisa de Vídeo do Bing'
titleSuffix: Azure Cognitive Services
description: Use este guia de início rápido para enviar solicitações de pesquisa de vídeo usando o SDK do Pesquisa de Vídeo do Bing para Python
services: cognitive-services
author: aahill
manager: nitinme
ms.service: cognitive-services
ms.subservice: bing-video-search
ms.topic: quickstart
ms.date: 06/26/2019
ms.author: aahi
ms.openlocfilehash: 62f929f3428ef7534d833e54b0b8f492636b8a7e
ms.sourcegitcommit: f523c8a8557ade6c4db6be12d7a01e535ff32f32
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 11/22/2019
ms.locfileid: "74378761"
---
# <a name="quickstart-perform-a-video-search-with-the-bing-video-search-sdk-for-python"></a>Início rápido: executar uma pesquisa de vídeo com o SDK do Pesquisa de Vídeo do Bing para Python

Use este guia de início rápido para começar a procurar notícias com o SDK do Pesquisa de Vídeo do Bing para Python. Embora Pesquisa de Vídeo do Bing tenha uma API REST compatível com a maioria das linguagens de programação, o SDK fornece uma maneira fácil de integrar o serviço em seus aplicativos. O código-fonte para este exemplo pode ser encontrado no [GitHub](https://github.com/Azure-Samples/cognitive-services-python-sdk-samples/blob/master/samples/search/video_search_samples.py) com anotações adicionais e recursos.

[!INCLUDE [cognitive-services-bing-video-search-signup-requirements](../../../../includes/cognitive-services-bing-video-search-signup-requirements.md)]

## <a name="prerequisites"></a>Pré-requisitos

- [Python](https://www.python.org/) 2. x ou 3. x
- O SDK do Pesquisa de Vídeo do Bing para Python

É recomendável que você use um [ambiente virtual](https://docs.python.org/3/tutorial/venv.html)do Python. Você pode instalar e inicializar um ambiente virtual com o [módulo venv](https://pypi.python.org/pypi/virtualenv). Instale o virtualenv para Python 2,7 com:

```console
python -m venv mytestenv
```

Instale o SDK do Pesquisa de Vídeo do Bing com:

```console
cd mytestenv
python -m pip install azure-cognitiveservices-search-videosearch
```

## <a name="create-and-initialize-the-application"></a>Criar e inicializar a aplicação

1. Crie um novo arquivo Python em seu IDE ou editor favorito e adicione as seguintes instruções de importação. 

    ```python
    from azure.cognitiveservices.search.videosearch import VideoSearchAPI
    from azure.cognitiveservices.search.videosearch.models import VideoPricing, VideoLength, VideoResolution, VideoInsightModule
    from msrest.authentication import CognitiveServicesCredentials
    ```

2. Crie uma variável para sua chave de assinatura. 

    ```python
    subscription_key = "YOUR-SUBSCRIPTION-KEY"
    ```

## <a name="create-the-search-client"></a>Criar o cliente de pesquisa

Crie uma instância do `CognitiveServicesCredentials`e instancie o cliente:

```python
client = VideoSearchAPI(CognitiveServicesCredentials(subscription_key))
```

## <a name="send-a-search-request-and-get-a-response"></a>Enviar uma solicitação de pesquisa e obter uma resposta

1. Use `client.videos.search()` com sua consulta de pesquisa para enviar uma solicitação ao API de Pesquisa de Vídeo do Bing e obter uma resposta.

    ```python
    video_result = client.videos.search(query="SwiftKey")
    ```

2. Se a resposta contiver resultados da pesquisa, obtenha o primeiro e imprima sua ID, nome e URL.

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
> [Criar um aplicativo Web de página única](../tutorial-bing-video-search-single-page-app.md)

## <a name="see-also"></a>Consulte também 

- [O que é o API de Pesquisa de Vídeo do Bing?](../overview.md)
- [Exemplos de SDK .NET nos serviços cognitivos](https://github.com/Azure-Samples/cognitive-services-dotnet-sdk-samples/tree/master/BingSearchv7)
