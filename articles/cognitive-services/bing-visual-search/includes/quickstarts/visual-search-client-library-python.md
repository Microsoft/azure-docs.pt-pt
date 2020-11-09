---
title: Bing Visual Search Python biblioteca cliente quickstart
titleSuffix: Azure Cognitive Services
services: cognitive-services
author: aahill
manager: nitinme
ms.service: cognitive-services
ms.topic: include
ms.date: 03/26/2020
ms.author: aahi
ms.openlocfilehash: 1d7664160f1cef405f791628bad0b4e76e3c50f8
ms.sourcegitcommit: 22da82c32accf97a82919bf50b9901668dc55c97
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 11/08/2020
ms.locfileid: "94371852"
---
Utilize este quickstart para começar a obter insights de imagem do serviço de Pesquisa Visual Bing, utilizando a biblioteca do cliente Python. Embora a Bing Visual Search tenha uma API REST compatível com a maioria das linguagens de programação, a biblioteca do cliente proporciona uma forma fácil de integrar o serviço nas suas aplicações. O código-fonte desta amostra pode ser encontrado no [GitHub](https://github.com/Azure-Samples/cognitive-services-python-sdk-samples/blob/master/samples/search/visual_search_samples.py) 

[Documentação de referência](/python/api/azure-cognitiveservices-search-visualsearch/?view=azure-python)  |  [Código fonte da biblioteca](https://github.com/Azure/azure-sdk-for-python/tree/master/sdk/cognitiveservices/azure-cognitiveservices-search-visualsearch)  |  [Pacote (PyPi)](https://pypi.org/project/azure-cognitiveservices-search-visualsearch/)  |  [Amostras](https://github.com/Azure-Samples/cognitive-services-python-sdk-samples/)

## <a name="prerequisites"></a>Pré-requisitos

* [Pitão](https://www.python.org/) 2.x ou 3.x
* Recomenda-se a utilização de um [ambiente virtual.](https://docs.python.org/3/tutorial/venv.html) Instale e inicialize o ambiente virtual com o [módulo venv](https://pypi.python.org/pypi/virtualenv).
* A biblioteca de clientes Bing Visual Search para Python. Pode instalá-lo com os seguintes comandos:
    1. `cd mytestenv`
    2. `python -m pip install azure-cognitiveservices-search-visualsearch`

[!INCLUDE [cognitive-services-bing-visual-search-signup-requirements](~/includes/cognitive-services-bing-visual-search-signup-requirements.md)]


## <a name="create-and-initialize-the-application"></a>Criar e inicializar a aplicação

1. Crie um novo ficheiro Python no seu IDE ou editor favorito e adicione as seguintes declarações de importação. 

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
    from msrest.authentication import CognitiveServicesCredentials
    ```
2. Crie variáveis para a sua chave de subscrição, ID de configuração personalizada e a imagem que pretende carregar. 
    
    ```python
    subscription_key = 'YOUR-VISUAL-SEARCH-ACCESS-KEY'
    PATH = 'C:\\Users\\USER\\azure-cognitive-samples\\mytestenv\\TestImages\\'
    image_path = os.path.join(PATH, "image.jpg")
    
    ```

3. Instanciar o cliente

    ```python
    client = VisualSearchClient(endpoint="https://api.cognitive.microsoft.com", credentials=CognitiveServicesCredentials(subscription_key))
    ```

## <a name="send-the-search-request"></a>Enviar o pedido de pesquisa

1. Com o ficheiro de imagem aberto, `VisualSearchRequest()` serialize, e passe-o como `knowledge_request` parâmetro para o `visual_search()` .

    ```python
    with open(image_path, "rb") as image_fd:
        # You need to pass the serialized form of the model
        knowledge_request = json.dumps(VisualSearchRequest().serialize())
    
        print("\r\nSearch visual search request with binary of dog image")
        result = client.images.visual_search(image=image_fd, knowledge_request=knowledge_request)
    ```

2. Se os resultados forem devolvidos, imprima-os, as etiquetas e as ações na primeira etiqueta.

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

## <a name="next-steps"></a>Passos seguintes

> [!div class="nextstepaction"]
> [Criar uma aplicação web de página única](../../tutorial-bing-visual-search-single-page-app.md)