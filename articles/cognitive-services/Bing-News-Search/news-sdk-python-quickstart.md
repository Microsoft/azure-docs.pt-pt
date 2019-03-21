---
title: 'Início rápido: Efetua uma pesquisa de notícias - SDK de pesquisa de notícias do Bing para Python'
titleSuffix: Azure Cognitive Services
description: Utilize este guia de introdução para pesquisar notícias com o SDK de pesquisa de notícias do Bing para Python e processar a resposta.
services: cognitive-services
author: mikedodaro
manager: nitinme
ms.service: cognitive-services
ms.subservice: bing-news-search
ms.topic: quickstart
ms.date: 01/10/2019
ms.author: v-gedod
ms.custom: seodec2018
ms.openlocfilehash: 1d6f4bb2bda550c72566243b2d2d98b1685e9147
ms.sourcegitcommit: 5839af386c5a2ad46aaaeb90a13065ef94e61e74
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/19/2019
ms.locfileid: "58119807"
---
# <a name="quickstart-perform-a-news-search-with-the-bing-news-search-sdk-for-python"></a>Início rápido: Efetua uma pesquisa de notícias com o SDK de pesquisa de notícias do Bing para Python

Utilize este guia de introdução para a pesquisa de notícias com o SDK de pesquisa de notícias do Bing para Python. Embora a pesquisa do Bing notícias tenha uma API de REST compatível com a maioria das linguagens de programação, o SDK fornece uma forma fácil de integrar o serviço aos seus aplicativos. O código-fonte para este exemplo pode ser encontrado no [GitHub](https://github.com/Azure-Samples/cognitive-services-python-sdk-samples/blob/master/samples/search/news_search_samples.py).

## <a name="prerequisites"></a>Pré-requisitos

* [Python](https://www.python.org/) 2.x ou 3.x

É recomendado utilizar um [ambiente virtual](https://docs.python.org/3/tutorial/venv.html) para o desenvolvimento de python. Pode instalar e inicializar o ambiente virtual com o [venv módulo](https://pypi.python.org/pypi/virtualenv). Tem de instalar um virtualenv para o Python 2.7. Pode criar um ambiente virtual com:

```console
python -m venv mytestenv
```

Pode instalar as dependências de SDK de pesquisa de notícias do Bing com este comando:
    
```console
python -m pip install azure-cognitiveservices-search-newssearch
```

[!INCLUDE [cognitive-services-bing-news-search-signup-requirements](../../../includes/cognitive-services-bing-news-search-signup-requirements.md)]

## <a name="create-and-initialize-the-application"></a>Criar e inicializar a aplicação

1. Criar um novo ficheiro de Python no seu IDE ou editor favorito e importar as seguintes bibliotecas. Crie uma variável para a chave de subscrição e o termo de pesquisa.

    ```python
    from azure.cognitiveservices.search.newssearch import NewsSearchAPI
    from msrest.authentication import CognitiveServicesCredentials
    subscription_key = "YOUR-SUBSCRIPTION-KEY"
    search_term = "Quantum Computing"
    ```

## <a name="initialize-the-client-and-send-a-request"></a>Inicializar o cliente e enviar um pedido

1. Crie uma instância de `CognitiveServicesCredentials`. Instancie o cliente:
    
    ```python
    client = NewsSearchAPI(CognitiveServicesCredentials(subscription_key))
    ```

2. Enviar uma consulta de pesquisa para a API de pesquisa de notícias, armazenar a resposta.

    ```python
    news_result = client.news.search(query=search_term, market="en-us", count=10)
    ```

## <a name="parse-the-response"></a>Analisar a resposta

Se forem encontrados quaisquer resultados de pesquisa, o primeiro resultado de página Web de impressão:

```python
if news_result.value:
    first_news_result = news_result.value[0]
    print("Total estimated matches value: {}".format(news_result.total_estimated_matches))
    print("News result count: {}".format(len(news_result.value)))
    print("First news name: {}".format(first_news_result.name))
    print("First news url: {}".format(first_news_result.url))
    print("First news description: {}".format(first_news_result.description))
    print("First published time: {}".format(first_news_result.date_published))
    print("First news provider: {}".format(first_news_result.provider[0].name))
else:
    print("Didn't see any news result data..")
```

## <a name="next-steps"></a>Passos Seguintes

> [!div class="nextstepaction"]
> [Criar uma aplicação Web de página única](tutorial-bing-news-search-single-page-app.md)
