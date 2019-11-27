---
title: 'Início rápido: executar uma pesquisa de notícias usando o SDK para Python-Pesquisa de Notícias do Bing'
titleSuffix: Azure Cognitive Services
description: Use este guia de início rápido para pesquisar notícias usando o SDK do Pesquisa de Notícias do Bing para Python e processar a resposta.
services: cognitive-services
author: aahill
manager: nitinme
ms.service: cognitive-services
ms.subservice: bing-news-search
ms.topic: quickstart
ms.date: 06/18/2019
ms.author: aahi
ms.custom: seodec2018
ms.openlocfilehash: 2f3d8c88e7949265f12405760acbccebb89d1df5
ms.sourcegitcommit: f523c8a8557ade6c4db6be12d7a01e535ff32f32
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 11/22/2019
ms.locfileid: "74379061"
---
# <a name="quickstart-perform-a-news-search-with-the-bing-news-search-sdk-for-python"></a>Início rápido: executar uma pesquisa de notícias com o SDK do Pesquisa de Notícias do Bing para Python

Use este guia de início rápido para começar a procurar notícias com o SDK do Pesquisa de Notícias do Bing para Python. Embora Pesquisa de Notícias do Bing tenha uma API REST compatível com a maioria das linguagens de programação, o SDK fornece uma maneira fácil de integrar o serviço em seus aplicativos. O código-fonte para este exemplo pode ser encontrado no [GitHub](https://github.com/Azure-Samples/cognitive-services-python-sdk-samples/blob/master/samples/search/news_search_samples.py).

## <a name="prerequisites"></a>Pré-requisitos

* [Python](https://www.python.org/) 2. x ou 3. x

É recomendável usar um [ambiente virtual](https://docs.python.org/3/tutorial/venv.html) para o desenvolvimento do Python. Você pode instalar e inicializar o ambiente virtual com o [módulo venv](https://pypi.python.org/pypi/virtualenv). Você deve instalar um virtualenv para Python 2,7. Você pode criar um ambiente virtual com:

```console
python -m venv mytestenv
```

Você pode instalar as dependências do SDK do Pesquisa de Notícias do Bing com este comando:
    
```console
python -m pip install azure-cognitiveservices-search-newssearch
```

[!INCLUDE [cognitive-services-bing-news-search-signup-requirements](../../../includes/cognitive-services-bing-news-search-signup-requirements.md)]

## <a name="create-and-initialize-the-application"></a>Criar e inicializar a aplicação

1. Crie um novo arquivo Python em seu IDE ou editor favorito e importe as bibliotecas a seguir. Crie uma variável para sua chave de assinatura e o termo de pesquisa.

    ```python
    from azure.cognitiveservices.search.newssearch import NewsSearchAPI
    from msrest.authentication import CognitiveServicesCredentials
    subscription_key = "YOUR-SUBSCRIPTION-KEY"
    search_term = "Quantum Computing"
    ```

## <a name="initialize-the-client-and-send-a-request"></a>Inicializar o cliente e enviar uma solicitação

1. Crie uma instância de `CognitiveServicesCredentials`. Instancie o cliente:
    
    ```python
    client = NewsSearchAPI(CognitiveServicesCredentials(subscription_key))
    ```

2. Envie uma consulta de pesquisa para a API Pesquisa de Notícias, armazene a resposta.

    ```python
    news_result = client.news.search(query=search_term, market="en-us", count=10)
    ```

## <a name="parse-the-response"></a>Analisar a resposta

Se forem encontrados resultados da pesquisa, imprima o primeiro resultado da página da Web:

```python
if news_result.value:
    first_news_result = news_result.value[0]
    print("Total estimated matches value: {}".format(
        news_result.total_estimated_matches))
    print("News result count: {}".format(len(news_result.value)))
    print("First news name: {}".format(first_news_result.name))
    print("First news url: {}".format(first_news_result.url))
    print("First news description: {}".format(first_news_result.description))
    print("First published time: {}".format(first_news_result.date_published))
    print("First news provider: {}".format(first_news_result.provider[0].name))
else:
    print("Didn't see any news result data..")
```

## <a name="next-steps"></a>Passos seguintes

> [!div class="nextstepaction"]
> [Criar uma aplicação Web de página única](tutorial-bing-news-search-single-page-app.md)
