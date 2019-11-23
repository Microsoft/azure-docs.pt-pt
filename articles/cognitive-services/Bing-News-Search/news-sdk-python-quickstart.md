---
title: 'Quickstart: Perform a news search using the SDK for Python - Bing News Search'
titleSuffix: Azure Cognitive Services
description: Use this quickstart to search for news using the Bing News Search SDK for Python, and process the response.
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
# <a name="quickstart-perform-a-news-search-with-the-bing-news-search-sdk-for-python"></a>Quickstart: Perform a news search with the Bing News Search SDK for Python

Use this quickstart to begin searching for news with the Bing News Search SDK for Python. While Bing News Search has a REST API compatible with most programming languages, the SDK provides an easy way to integrate the service into your applications. The source code for this sample can be found on [GitHub](https://github.com/Azure-Samples/cognitive-services-python-sdk-samples/blob/master/samples/search/news_search_samples.py).

## <a name="prerequisites"></a>Pré-requisitos

* [Python](https://www.python.org/) 2.x or 3.x

It is recommended to use a [virtual environment](https://docs.python.org/3/tutorial/venv.html) for your python development. You can install and initialize the virtual environment with the [venv module](https://pypi.python.org/pypi/virtualenv). You must install a virtualenv for Python 2.7. You can create a virtual environment with:

```console
python -m venv mytestenv
```

You can install the Bing News Search SDK dependencies with this command:
    
```console
python -m pip install azure-cognitiveservices-search-newssearch
```

[!INCLUDE [cognitive-services-bing-news-search-signup-requirements](../../../includes/cognitive-services-bing-news-search-signup-requirements.md)]

## <a name="create-and-initialize-the-application"></a>Criar e inicializar a aplicação

1. Create a new Python file in your favorite IDE or editor, and import the following libraries. Create a variable for your subscription key, and your search term.

    ```python
    from azure.cognitiveservices.search.newssearch import NewsSearchAPI
    from msrest.authentication import CognitiveServicesCredentials
    subscription_key = "YOUR-SUBSCRIPTION-KEY"
    search_term = "Quantum Computing"
    ```

## <a name="initialize-the-client-and-send-a-request"></a>Initialize the client and send a request

1. Crie uma instância de `CognitiveServicesCredentials`. Instancie o cliente:
    
    ```python
    client = NewsSearchAPI(CognitiveServicesCredentials(subscription_key))
    ```

2. Send a search query to the News Search API, store the response.

    ```python
    news_result = client.news.search(query=search_term, market="en-us", count=10)
    ```

## <a name="parse-the-response"></a>Parse the response

If any search results are found, print the first webpage result:

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
