---
title: 'Quickstart: Pesquisa de entidades com o SDK para Python - Bing Entity Search'
titleSuffix: Azure Cognitive Services
description: Use este quickstart para procurar entidades com o Bing Entity Search SDK for Python.
services: cognitive-services
author: aahill
manager: nitinme
ms.service: cognitive-services
ms.subservice: bing-entity-search
ms.topic: quickstart
ms.date: 02/11/2020
ms.author: aahi
ms.openlocfilehash: a6b62f7ab95f7b2720434c0cf59cce33b0adb1b4
ms.sourcegitcommit: 333af18fa9e4c2b376fa9aeb8f7941f1b331c11d
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 02/13/2020
ms.locfileid: "77201242"
---
# <a name="quickstart-bing-entity-search-sdk-with-python"></a>Início Rápido: SDK de Pesquisa de Entidades do Bing com Python

Use este quickstart para começar a procurar entidades com o Bing Entity Search SDK for Python. Embora a Bing Entity Search tenha um Rest API compatível com a maioria dos idiomas de programação, o SDK fornece uma forma fácil de integrar o serviço nas suas aplicações. O código fonte desta amostra pode ser encontrado no [GitHub](https://github.com/Azure-Samples/cognitive-services-python-sdk-samples/blob/master/samples/search/entity_search_samples.py).

## <a name="prerequisites"></a>Pré-requisitos

* Python [2.x ou 3.x](https://www.python.org/)

* O [Bing Entity Search SDK for Python](https://pypi.org/project/azure-cognitiveservices-search-entitysearch/)

Recomenda-se que utilize um ambiente virtual python. Pode instalar e inicializar um ambiente virtual com o módulo venv. Pode instalar o virtualenv com:

```Console
python -m venv mytestenv
```

Instale o SDK de Pesquisa de Entidades Bing com:

```Console
cd mytestenv
python -m pip install azure-cognitiveservices-search-entitysearch
```

[!INCLUDE [cognitive-services-bing-news-search-signup-requirements](../../../../includes/cognitive-services-bing-entity-search-signup-requirements.md)]

## <a name="create-and-initialize-the-application"></a>Criar e inicializar a aplicação

1. Crie um novo ficheiro Python no seu IDE ou editor favorito e adicione as seguintes declarações de importação. 

    ```python
    from azure.cognitiveservices.search.entitysearch import EntitySearchClient
    from azure.cognitiveservices.search.entitysearch.models import Place, ErrorResponseException
    from msrest.authentication import CognitiveServicesCredentials
    ```

2. Crie uma variável para a sua chave de subscrição e ponto final. Instantifique o cliente criando um novo objeto de `CognitiveServicesCredentials` com a sua chave.
    
    ```python
    subscription_key = "YOUR-SUBSCRIPTION-KEY"
    endpoint = "YOUR-ENDPOINT"
    client = EntitySearchclient(endpoint=endpoint, credentials=CognitiveServicesCredentials(subscription_key))
    ```

## <a name="send-a-search-request-and-receive-a-response"></a>Envie um pedido de pesquisa e receba uma resposta

1. Envie um pedido de pesquisa para a Bing Entity Search com `client.entities.search()` e uma consulta de pesquisa. 
    
    ```python
    entity_data = client.entities.search(query="Gibralter")
    ```

2. Se as entidades forem devolvidas, converta `entity_data.entities.value` para uma lista e imprima o primeiro resultado.
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

* [O que é a API de Pesquisa de Entidades Bing?](../overview.md )