---
title: 'Início rápido: Pesquisar entidades com o SDK para Python-Pesquisa de Entidade do Bing'
titleSuffix: Azure Cognitive Services
description: Use este guia de início rápido para pesquisar entidades com o SDK do Pesquisa de Entidade do Bing para Python.
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

Use este guia de início rápido para começar a procurar entidades com o SDK do Pesquisa de Entidade do Bing para Python. Embora Pesquisa de Entidade do Bing tenha uma API REST compatível com a maioria das linguagens de programação, o SDK fornece uma maneira fácil de integrar o serviço em seus aplicativos. O código-fonte para este exemplo pode ser encontrado no [GitHub](https://github.com/Azure-Samples/cognitive-services-python-sdk-samples/blob/master/samples/search/entity_search_samples.py).

## <a name="prerequisites"></a>Pré-requisitos

* Python [2. x ou 3. x](https://www.python.org/)

* O [SDK do pesquisa de entidade do Bing para Python](https://pypi.org/project/azure-cognitiveservices-search-entitysearch/)

É recomendável que você use um ambiente virtual do Python. Você pode instalar e inicializar um ambiente virtual com o módulo venv. Você pode instalar o virtualenv com:

```Console
python -m venv mytestenv
```

Instale o SDK do Pesquisa de Entidade do Bing com:

```Console
cd mytestenv
python -m pip install azure-cognitiveservices-search-entitysearch
```

[!INCLUDE [cognitive-services-bing-news-search-signup-requirements](../../../../includes/cognitive-services-bing-entity-search-signup-requirements.md)]

## <a name="create-and-initialize-the-application"></a>Criar e inicializar a aplicação

1. Crie um novo arquivo Python em seu IDE ou editor favorito e adicione as seguintes instruções de importação. 

    ```python
    from azure.cognitiveservices.search.entitysearch import EntitySearchAPI
    from azure.cognitiveservices.search.entitysearch.models import Place, ErrorResponseException
    from msrest.authentication import CognitiveServicesCredentials
    ```

2. Crie uma variável para sua chave de assinatura e instancie o cliente criando um novo objeto `CognitiveServicesCredentials` com ele.
    
    ```python
    subscription_key = "YOUR-SUBSCRIPTION-KEY"
    client = EntitySearchAPI(CognitiveServicesCredentials(subscription_key))
    ```

## <a name="send-a-search-request-and-receive-a-response"></a>Enviar uma solicitação de pesquisa e receber uma resposta

1. Envie uma solicitação de pesquisa para Pesquisa de Entidade do Bing com `client.entities.search()` e uma consulta de pesquisa. 
    
    ```python
    entity_data = client.entities.search(query="Gibralter")
    ```

2. Se as entidades forem retornadas, converta `entity_data.entities.value` em uma lista e imprima o primeiro resultado.
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

* [O que é o API de Pesquisa de Entidade do Bing?](../overview.md )