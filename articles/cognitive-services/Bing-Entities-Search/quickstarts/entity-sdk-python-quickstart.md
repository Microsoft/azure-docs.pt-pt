---
title: 'Quickstart: Search for entities with the SDK for Python - Bing Entity Search'
titleSuffix: Azure Cognitive Services
description: Use this quickstart to search for entities with the Bing Entity Search SDK for Python.
services: cognitive-services
author: aahill
manager: nitinme
ms.service: cognitive-services
ms.subservice: bing-entity-search
ms.topic: quickstart
ms.date: 07/24/2019
ms.author: aahi
ms.openlocfilehash: 7720304cc017db86b052cee39ca0430e5c1bf9c2
ms.sourcegitcommit: b77e97709663c0c9f84d95c1f0578fcfcb3b2a6c
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 11/22/2019
ms.locfileid: "74327128"
---
# <a name="quickstart-bing-entity-search-sdk-with-python"></a>Início Rápido: SDK de Pesquisa de Entidades do Bing com Python

Use this quickstart to begin searching for entities with the Bing Entity Search SDK for Python. While Bing Entity Search has a REST API compatible with most programming languages, the SDK provides an easy way to integrate the service into your applications. The source code for this sample can be found on [GitHub](https://github.com/Azure-Samples/cognitive-services-python-sdk-samples/blob/master/samples/search/entity_search_samples.py).

## <a name="prerequisites"></a>Pré-requisitos

* Python [2.x or 3.x](https://www.python.org/)

* The [Bing Entity Search SDK for Python](https://pypi.org/project/azure-cognitiveservices-search-entitysearch/)

It is recommended that you use a python virtual environment. You can install and initialize a virtual environment with the venv module. You can install virtualenv with:

```Console
python -m venv mytestenv
```

Install the Bing Entity Search SDK with:

```Console
cd mytestenv
python -m pip install azure-cognitiveservices-search-entitysearch
```

[!INCLUDE [cognitive-services-bing-news-search-signup-requirements](../../../../includes/cognitive-services-bing-entity-search-signup-requirements.md)]

## <a name="create-and-initialize-the-application"></a>Criar e inicializar a aplicação

1. Create a new Python file in your favorite IDE or editor, and add the following import statements. 

    ```python
    from azure.cognitiveservices.search.entitysearch import EntitySearchAPI
    from azure.cognitiveservices.search.entitysearch.models import Place, ErrorResponseException
    from msrest.authentication import CognitiveServicesCredentials
    ```

2. Create a variable for your subscription key, and instantiate the client by creating a new `CognitiveServicesCredentials` object with it.
    
    ```python
    subscription_key = "YOUR-SUBSCRIPTION-KEY"
    client = EntitySearchAPI(CognitiveServicesCredentials(subscription_key))
    ```

## <a name="send-a-search-request-and-receive-a-response"></a>Send a search request and receive a response

1. Send a search request to Bing Entity Search with `client.entities.search()` and a search query. 
    
    ```python
    entity_data = client.entities.search(query="Gibralter")
    ```

2. If entities were returned, convert `entity_data.entities.value` to a list, and print the first result.
    ```python
    if entity_data.entities.value:
    
        main_entities = [entity for entity in entity_data.entities.value
                         if entity.entity_presentation_info.entity_scenario == "DominantEntity"]
    
        if main_entities:
            print(main_entities[0].description)
    ```

## <a name="next-steps"></a>Passos seguintes

> [!div class="nextstepaction"]
> [Criar uma aplicação Web de página única](../tutorial-bing-entities-search-single-page-app.md)

* [What is the Bing Entity Search API?](../overview.md )