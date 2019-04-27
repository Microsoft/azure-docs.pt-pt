---
title: 'Início rápido: Pesquisa de entidades do Bing SDK, Python'
titlesuffix: Azure Cognitive Services
description: Configure a aplicação de consola do SDK de Pesquisa de Entidades do Bing.
services: cognitive-services
author: mikedodaro
manager: nitinme
ms.service: cognitive-services
ms.subservice: bing-entity-search
ms.topic: quickstart
ms.date: 02/01/2019
ms.author: v-gedod
ms.openlocfilehash: a2d901c1cec4556e4061c21548d7a695c06e8cf8
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/23/2019
ms.locfileid: "60737096"
---
# <a name="quickstart-bing-entity-search-sdk-with-python"></a>Início rápido: Pesquisa de entidades do Bing SDK com Python

Utilize este guia de introdução para começar a procurar entidades com o SDK de pesquisa de entidades do Bing para Python. Embora a pesquisa de entidades do Bing tenha uma API de REST compatível com a maioria das linguagens de programação, o SDK fornece uma forma fácil de integrar o serviço aos seus aplicativos. O código-fonte para este exemplo pode ser encontrado no [GitHub](https://github.com/Azure-Samples/cognitive-services-python-sdk-samples/blob/master/samples/search/entity_search_samples.py).

## <a name="prerequisites"></a>Pré-requisitos

* Python [2.x ou 3.x](https://www.python.org/)

* O [de pesquisa de entidades do Bing SDK para Python](https://pypi.org/project/azure-cognitiveservices-search-entitysearch/)

Recomenda-se que utilize um ambiente virtual do python. Pode instalar e inicializar um ambiente virtual com o módulo de venv. Pode instalar virtualenv para o Python 2.7 com:

```Console
python -m venv mytestenv
```

Instalar o SDK com de pesquisa de entidades do Bing:

```Console
cd mytestenv
python -m pip install azure-cognitiveservices-search-entitysearch
```

[!INCLUDE [cognitive-services-bing-news-search-signup-requirements](../../../../includes/cognitive-services-bing-entity-search-signup-requirements.md)]

## <a name="create-and-initialize-the-application"></a>Criar e inicializar a aplicação

1. Criar um novo ficheiro de Python no seu IDE ou editor favorito e adicione as seguintes declarações de importação. 

    ```python
    from azure.cognitiveservices.search.entitysearch import EntitySearchAPI
    from azure.cognitiveservices.search.entitysearch.models import Place, ErrorResponseException
    from msrest.authentication import CognitiveServicesCredentials
    ```

2. Criar uma variável para a sua chave de subscrição e criar uma instância do cliente ao criar um novo `CognitiveServicesCredentials` objeto com o mesmo.
    
    ```python
    subscription_key = "YOUR-SUBSCRIPTION-KEY"
    client = EntitySearchAPI(CognitiveServicesCredentials(subscription_key))
    ```

## <a name="send-a-search-request-and-receive-a-response"></a>Enviar um pedido de pesquisa e receber uma resposta

1. Enviar um pedido de pesquisa para pesquisa de entidades do Bing com `client.entities.search()` e uma consulta de pesquisa. 
    
    ```python
    entity_data = client.entities.search(query="Gibralter")
    ```

2. Se a entidades foram devolvidas, converter `entity_data.entities.value` a uma lista e imprimir o primeiro resultado.
    ```python
    if entity_data.entities.value:
    
        main_entities = [entity for entity in entity_data.entities.value
                         if entity.entity_presentation_info.entity_scenario == "DominantEntity"]
    
        if main_entities:
            print(main_entities[0].description)
    ```

## <a name="next-steps"></a>Passos Seguintes

> [!div class="nextstepaction"]
> [Criar uma aplicação Web de página única](../tutorial-bing-entities-search-single-page-app.md)

* [O que é a API de pesquisa de entidades do Bing?](../overview.md )