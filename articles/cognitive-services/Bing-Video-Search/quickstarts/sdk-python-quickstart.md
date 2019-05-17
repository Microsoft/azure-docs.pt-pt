---
title: 'Início rápido: Procurar vídeos com o SDK de pesquisa de vídeos do Bing para Python'
titleSuffix: Azure Cognitive Services
description: Utilize este guia de introdução para enviar pedidos de pesquisa de vídeos com o SDK de pesquisa de vídeos do Bing para Python
services: cognitive-services
author: aahill
manager: nitinme
ms.service: cognitive-services
ms.subservice: bing-video-search
ms.topic: quickstart
ms.date: 01/31/2019
ms.author: aahi
ms.openlocfilehash: ae91deb5b49b0eb202175fdd242ef0d91975bb25
ms.sourcegitcommit: 36c50860e75d86f0d0e2be9e3213ffa9a06f4150
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 05/16/2019
ms.locfileid: "65798230"
---
#  <a name="quickstart-perform-a-video-search-with-the-bing-video-search-sdk-for-python"></a>Início rápido: Executar uma pesquisa de vídeos com o SDK de pesquisa de vídeos do Bing para Python

Utilize este guia de introdução para a pesquisa de notícias com o SDK de pesquisa de vídeos do Bing para Python. Embora a pesquisa de vídeos do Bing tenha uma API de REST compatível com a maioria das linguagens de programação, o SDK fornece uma forma fácil de integrar o serviço aos seus aplicativos. O código-fonte para este exemplo pode ser encontrado no [GitHub](https://github.com/Azure-Samples/cognitive-services-python-sdk-samples/blob/master/samples/search/video_search_samples.py). Ela contém mais anotações e funcionalidades.

[!INCLUDE [cognitive-services-bing-video-search-signup-requirements](../../../../includes/cognitive-services-bing-video-search-signup-requirements.md)]

## <a name="prerequisites"></a>Pré-requisitos

- [Python](https://www.python.org/) 2.x ou 3.x
- O SDK de pesquisa de vídeos do Bing para python

É recomendado que utilize um python [ambiente virtual](https://docs.python.org/3/tutorial/venv.html). Pode instalar e inicializar um ambiente virtual com o [venv módulo](https://pypi.python.org/pypi/virtualenv). Instale virtualenv para o Python 2.7 com:

```console
python -m venv mytestenv
```
   
Instalar o SDK com de pesquisa de vídeos do Bing:

```console
cd mytestenv
python -m pip install azure-cognitiveservices-search-videosearch
```

## <a name="create-and-initialize-the-application"></a>Criar e inicializar a aplicação

1. Criar um novo ficheiro de Python no seu IDE ou editor favorito e adicione as seguintes declarações de importação. 

    ```python
    from azure.cognitiveservices.search.videosearch import VideoSearchAPI
    from azure.cognitiveservices.search.videosearch.models import VideoPricing, VideoLength, VideoResolution, VideoInsightModule
    from msrest.authentication import CognitiveServicesCredentials
    ```
2. Crie uma variável para a sua chave de subscrição. 
    
    ```python
    subscription_key = "YOUR-SUBSCRIPTION-KEY"
    ```

## <a name="create-the-search-client"></a>Criar o cliente de pesquisa

Crie uma instância do `CognitiveServicesCredentials`e instancie o cliente:

    ```python
    client = VideoSearchAPI(CognitiveServicesCredentials(subscription_key))
    ```

## <a name="send-a-search-request-and-get-a-response"></a>Enviar um pedido de pesquisa e obter uma resposta

1. Utilize `client.videos.search()` com a sua consulta de pesquisa para enviar um pedido para a API de pesquisa de vídeos do Bing e obter uma resposta.

    ```python
    video_result = client.videos.search(query="SwiftKey")
    ```

2. Se a resposta contém os resultados da pesquisa, obtenha primeiro e imprimir o respetivo ID, name e url.

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

## <a name="next-steps"></a>Passos Seguintes

> [!div class="nextstepaction"]
> [Criar uma aplicação web de página única](../tutorial-bing-video-search-single-page-app.md)

## <a name="see-also"></a>Consulte também 

* [O que é a API de pesquisa de vídeos do Bing?](../overview.md)
* [Exemplos de SDK .NET nos serviços cognitivos](https://github.com/Azure-Samples/cognitive-services-dotnet-sdk-samples/tree/master/BingSearchv7)