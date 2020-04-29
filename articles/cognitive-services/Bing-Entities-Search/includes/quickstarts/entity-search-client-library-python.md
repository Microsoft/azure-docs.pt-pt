---
title: Bing Entity Search Python biblioteca de clientes quickstart
titleSuffix: Azure Cognitive Services
services: cognitive-services
author: aahill
manager: nitinme
ms.service: cognitive-services
ms.topic: include
ms.date: 03/06/2020
ms.author: aahi
ms.openlocfilehash: 0c0a124773eab8166806312dc47ded24e1cd841f
ms.sourcegitcommit: 34a6fa5fc66b1cfdfbf8178ef5cdb151c97c721c
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/28/2020
ms.locfileid: "79136761"
---
Use este quickstart para começar a procurar entidades com a biblioteca de clientes Bing Entity Search para Python. Embora a Bing Entity Search tenha uma API REST compatível com a maioria dos idiomas de programação, a biblioteca do cliente fornece uma forma fácil de integrar o serviço nas suas aplicações. O código fonte desta amostra pode ser encontrado no [GitHub](https://github.com/Azure-Samples/cognitive-services-python-sdk-samples/blob/master/samples/search/entity_search_samples.py).

## <a name="prerequisites"></a>Pré-requisitos

* Python [2.x ou 3.x](https://www.python.org/)

* O [Bing Entity Search SDK for Python](https://pypi.org/project/azure-cognitiveservices-search-entitysearch/)

Recomenda-se que utilize um ambiente virtual python. Pode instalar e inicializar um ambiente virtual com o módulo venv. Pode instalar o virtualenv com:

```Console
python -m venv mytestenv
```

Instale a biblioteca cliente bing entity search com:

```Console
cd mytestenv
python -m pip install azure-cognitiveservices-search-entitysearch
```

[!INCLUDE [cognitive-services-bing-news-search-signup-requirements](~/includes/cognitive-services-bing-entity-search-signup-requirements.md)]

## <a name="create-and-initialize-the-application"></a>Criar e inicializar a aplicação

1. Crie um novo ficheiro Python no seu IDE ou editor favorito e adicione as seguintes declarações de importação. 

    ```python
    from azure.cognitiveservices.search.entitysearch import EntitySearchClient
    from azure.cognitiveservices.search.entitysearch.models import Place, ErrorResponseException
    from msrest.authentication import CognitiveServicesCredentials
    ```

2. Crie uma variável para a sua chave de subscrição e ponto final. Instantifique o cliente `CognitiveServicesCredentials` criando um novo objeto com a sua chave.
    
    ```python
    subscription_key = "YOUR-SUBSCRIPTION-KEY"
    endpoint = "YOUR-ENDPOINT"
    client = EntitySearchclient(endpoint=endpoint, credentials=CognitiveServicesCredentials(subscription_key))
    ```

## <a name="send-a-search-request-and-receive-a-response"></a>Envie um pedido de pesquisa e receba uma resposta

1. Envie um pedido de pesquisa `client.entities.search()` para a Pesquisa de Entidades Bing e uma consulta de pesquisa. 
    
    ```python
    entity_data = client.entities.search(query="Gibralter")
    ```

2. Se as entidades `entity_data.entities.value` foram devolvidas, converta-se numa lista e imprima o primeiro resultado.
    ```python
    if entity_data.entities.value:
    
        main_entities = [entity for entity in entity_data.entities.value
                         if entity.entity_presentation_info.entity_scenario == "DominantEntity"]
    
        if main_entities:
            print(main_entities[0].description)
    ```

## <a name="next-steps"></a>Passos seguintes

> [!div class="nextstepaction"]
> [Criar uma aplicação web de página única](../../tutorial-bing-entities-search-single-page-app.md)

* [O que é a API de Pesquisa de Entidades Bing?](../../overview.md )