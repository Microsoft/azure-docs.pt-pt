---
title: 'Início rápido: Pesquisa Visual do Bing SDK, Python'
titleSuffix: Azure Cognitive Services
description: Configure a aplicação de consola Python do SDK de Pesquisa Visual.
services: cognitive-services
author: aahill
manager: nitinme
ms.service: cognitive-services
ms.subservice: bing-visual-search
ms.topic: quickstart
ms.date: 06/11/2018
ms.author: v-gedod
ms.openlocfilehash: b2cb82076eb54f858fffb67d585ac2273f98583d
ms.sourcegitcommit: 36c50860e75d86f0d0e2be9e3213ffa9a06f4150
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 05/16/2019
ms.locfileid: "65796319"
---
# <a name="quickstart-get-image-insights-using-the-bing-visual-search-sdk-for-python"></a>Início rápido: Obtenha informações de imagem com o SDK de pesquisa Visual do Bing para Python

Utilize este guia de introdução para iniciar a obtenção de informações de imagem do serviço de pesquisa Visual do Bing, utilizando o SDK de Python. Embora a pesquisa Visual do Bing tenha uma API de REST compatível com a maioria das linguagens de programação, o SDK fornece uma forma fácil de integrar o serviço aos seus aplicativos. O código-fonte para este exemplo pode ser encontrado no [GitHub](https://github.com/Azure-Samples/cognitive-services-python-sdk-samples/blob/master/samples/search/visual_search_samples.py) 

## <a name="prerequisites"></a>Pré-requisitos

* [Python](https://www.python.org/) 2.x ou 3.x
* É recomendado utilizar um [ambiente virtual](https://docs.python.org/3/tutorial/venv.html). Instale e inicialize o ambiente virtual com o [módulo venv](https://pypi.python.org/pypi/virtualenv). Instale o virtualenv para Python 2.7.
* A pesquisa Visual do Bing SDK para Python. Pode instalá-lo com os seguintes comandos:
    1. `cd mytestenv`
    2. `python -m pip install azure-cognitiveservices-search-visualsearch`



[!INCLUDE [cognitive-services-bing-image-search-signup-requirements](../../../includes/cognitive-services-bing-image-search-signup-requirements.md)]


## <a name="create-and-initialize-the-application"></a>Criar e inicializar a aplicação

1. Criar um novo ficheiro de Python no seu IDE ou editor favorito e adicione as seguintes declarações de importação. 

    ```python
    import http.client, urllib.parse
    import json
    import os.path
    from azure.cognitiveservices.search.visualsearch import VisualSearchClient
    from azure.cognitiveservices.search.visualsearch.models import (
        VisualSearchRequest,
        CropArea,
        ImageInfo,
        Filters,
        KnowledgeRequest,
    )
    ```
2. Crie variáveis para a chave de subscrição, ID de configuração personalizada e a imagem que pretende carregar. 
    
    ```python
    subscription_key = 'YOUR-VISUAL-SEARCH-ACCESS-KEY'
    PATH = 'C:\\Users\\USER\\azure-cognitive-samples\\mytestenv\\TestImages\\'
    image_path = os.path.join(PATH, "image.jpg")
    
    ```

3. Instanciar o cliente

    ```python
    var client = new VisualSearchClient(new ApiKeyServiceClientCredentials("YOUR-ACCESS-KEY"))
    ```

## <a name="send-the-search-request"></a>Enviar a solicitação de pesquisa

1. Com a imagem de ficheiros abertos, serializar `VisualSearchRequest()`e passá-lo como o `knowledge_request` parâmetro para o `visual_search()`.

    ```python
    with open(image_path, "rb") as image_fd:
        # You need to pass the serialized form of the model
        knowledge_request = json.dumps(VisualSearchRequest().serialize())
    
        print("\r\nSearch visual search request with binary of dog image")
        result = client.images.visual_search(image=image_fd, knowledge_request=knowledge_request)
    ```

2. Se foram devolvidos resultados, imprimi-los, as etiquetas e as ações na primeira marca.

    ```python
    if not result:
            print("No visual search result data.")
    
            # Visual Search results
        if result.image.image_insights_token:
            print("Uploaded image insights token: {}".format(result.image.image_insights_token))
        else:
            print("Couldn't find image insights token!")
    
        # List of tags
        if result.tags:
            first_tag = result.tags[0]
            print("Visual search tag count: {}".format(len(result.tags)))
    
            # List of actions in first tag
            if first_tag.actions:
                first_tag_action = first_tag.actions[0]
                print("First tag action count: {}".format(len(first_tag.actions)))
                print("First tag action type: {}".format(first_tag_action.action_type))
            else:
                print("Couldn't find tag actions!")
        else:
            print("Couldn't find image tags!")
    ```

## <a name="next-steps"></a>Passos Seguintes

> [!div class="nextstepaction"]
> [Criar uma aplicação web de página única](tutorial-bing-visual-search-single-page-app.md)
