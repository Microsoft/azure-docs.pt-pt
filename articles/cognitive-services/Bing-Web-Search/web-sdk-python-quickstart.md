---
title: 'Início rápido: Utilizar a SDK de pesquisa Web Bing para Python'
titleSuffix: Azure Cognitive Services
description: O SDK de Pesquisa na Web do Bing facilita a integração da Pesquisa na Web do Bing na sua aplicação Python. Neste início rápido, vai aprender como enviar um pedido, receber uma resposta JSON e filtrar e analisar os resultados.
services: cognitive-services
author: aahill
manager: nitinme
ms.service: cognitive-services
ms.subservice: bing-web-search
ms.topic: quickstart
ms.date: 03/12/2019
ms.author: aahi
ms.openlocfilehash: 273922c8cf48c24ff3b1b55fa44b36b69e061057
ms.sourcegitcommit: 5839af386c5a2ad46aaaeb90a13065ef94e61e74
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/19/2019
ms.locfileid: "57863904"
---
# <a name="quickstart-use-the-bing-web-search-sdk-for-python"></a>Início rápido: Utilizar a SDK de pesquisa Web Bing para Python

O SDK de Pesquisa na Web do Bing facilita a integração da Pesquisa na Web do Bing na sua aplicação Python. Neste início rápido, vai aprender como enviar um pedido, receber uma resposta JSON e filtrar e analisar os resultados.

Quer ver o código imediatamente? Os [exemplos de SDK de Pesquisa na Web do Bing para Python](https://github.com/Azure-Samples/cognitive-services-python-sdk-samples) estão disponíveis para o GitHub.

[!INCLUDE [bing-web-search-quickstart-signup](../../../includes/bing-web-search-quickstart-signup.md)]

Consulte também [dos serviços cognitivos preços - API de pesquisa Bing](https://azure.microsoft.com/pricing/details/cognitive-services/search-api/).

## <a name="prerequisites"></a>Pré-requisitos
O SDK de Pesquisa na Web do Bing é compatível com o Python 2.7, 3.3, 3.4, 3.5 e 3.6. Recomendamos utilizar um ambiente virtual para este início rápido.

* Python 2.7, 3.3, 3.4, 3.5 ou 3.6
* [virtualenv](https://docs.python.org/3/tutorial/venv.html) para Python 2.7
* [venv](https://pypi.python.org/pypi/virtualenv) para Python 3.x

## <a name="create-and-configure-your-virtual-environment"></a>Criar e configurar o seu ambiente virtual

As instruções para instalar e configurar um ambiente virtual são diferentes para o Python 2.x e Python 3.x. Siga os passos abaixo para criar e inicializar o ambiente virtual.

### <a name="python-2x"></a>Python 2.x

Crie um ambiente virtual com `virtualenv` para Python 2.7:

```console
virtualenv mytestenv
```

Ativar o seu ambiente:

```console
cd mytestenv
source bin/activate
```

Instale as dependências do SDK de Pesquisa na Web do Bing:

```console
python -m pip install azure-cognitiveservices-search-websearch
```

### <a name="python-3x"></a>Python 3.x

Crie um ambiente virtual com `venv` para Python 3.x:

```console
python -m venv mytestenv
```

Instale as dependências do SDK de Pesquisa na Web do Bing:

```console
cd mytestenv
python -m pip install azure-cognitiveservices-search-websearch
```

## <a name="create-a-client-and-print-your-first-results"></a>Criar um cliente e imprimir os seus primeiros resultados

Agora que configurou o seu ambiente virtual e instalou dependências, vamos criar um cliente. O cliente irá processar pedidos para e respostas da API de Pesquisa na Web do Bing.

Se a resposta contiver páginas Web, imagens, notícias ou vídeos, será impresso o primeiro resultado para cada.

1. Crie um novo projeto do Python através do seu editor ou IDE favorito.

1. Copie este código de exemplo para o seu projeto:  

    ```python
    # Import required modules.
    from azure.cognitiveservices.search.websearch import WebSearchAPI
    from azure.cognitiveservices.search.websearch.models import SafeSearch
    from msrest.authentication import CognitiveServicesCredentials

    # Replace with your subscription key.
    subscription_key = "YOUR_SUBSCRIPTION_KEY"

    # Instantiate the client.
    client = WebSearchAPI(CognitiveServicesCredentials(subscription_key))

    # Make a request. Replace Yosemite if you'd like.
    web_data = client.web.search(query="Yosemite")
    print("\r\nSearched for Query# \" Yosemite \"")

    '''
    Web pages
    If the search response contains web pages, the first result's name and url
    are printed.
    '''
    if hasattr(web_data.web_pages, 'value'):

        print("\r\nWebpage Results#{}".format(len(web_data.web_pages.value)))

        first_web_page = web_data.web_pages.value[0]
        print("First web page name: {} ".format(first_web_page.name))
        print("First web page URL: {} ".format(first_web_page.url))

    else:
        print("Didn't find any web pages...")

    '''
    Images
    If the search response contains images, the first result's name and url
    are printed.
    '''
    if hasattr(web_data.images, 'value'):

        print("\r\nImage Results#{}".format(len(web_data.images.value)))

        first_image = web_data.images.value[0]
        print("First Image name: {} ".format(first_image.name))
        print("First Image URL: {} ".format(first_image.url))

    else:
        print("Didn't find any images...")

    '''
    News
    If the search response contains news, the first result's name and url
    are printed.
    '''
    if hasattr(web_data.news, 'value'):

        print("\r\nNews Results#{}".format(len(web_data.news.value)))

        first_news = web_data.news.value[0]
        print("First News name: {} ".format(first_news.name))
        print("First News URL: {} ".format(first_news.url))

    else:
        print("Didn't find any news...")

    '''
    If the search response contains videos, the first result's name and url
    are printed.
    '''
    if hasattr(web_data.videos, 'value'):

        print("\r\nVideos Results#{}".format(len(web_data.videos.value)))

        first_video = web_data.videos.value[0]
        print("First Videos name: {} ".format(first_video.name))
        print("First Videos URL: {} ".format(first_video.url))

    else:
        print("Didn't find any videos...")
    ```

1. Substitua `subscription_key` por uma chave de subscrição válida.

1. Execute o programa. Por exemplo: `python your_program.py`.

## <a name="define-functions-and-filter-results"></a>Definir funções e filtrar resultados

Agora que fez a sua primeira chamada para a API de pesquisa Web Bing, vamos examinar algumas funções. As secções seguintes realçam a funcionalidade SDK para refinar a consultas e filtragem de resultados. Cada função pode ser adicionada para o programa de Python que criou na secção anterior.

### <a name="limit-the-number-of-results-returned-by-bing"></a>Limitar o número de resultados devolvidos pelo Bing

Este exemplo utiliza os parâmetros `count` e `offset` para limitar o número de resultados devolvidos através do método [`search` do SDK](https://docs.microsoft.com/python/api/azure-cognitiveservices-search-websearch/azure.cognitiveservices.search.websearch.operations.weboperations?view=azure-python). O `name` e `url` para o primeiro resultado são impressos.

1. Adicione este código ao seu projeto Python:

   ```python
    # Declare the function.
    def web_results_with_count_and_offset(subscription_key):
        client = WebSearchAPI(CognitiveServicesCredentials(subscription_key))

        try:
            '''
            Set the query, offset, and count using the SDK's search method. See:
            https://docs.microsoft.com/python/api/azure-cognitiveservices-search-websearch/azure.cognitiveservices.search.websearch.operations.weboperations?view=azure-python.
            '''
            web_data = client.web.search(query="Best restaurants in Seattle", offset=10, count=20)
            print("\r\nSearching for \"Best restaurants in Seattle\"")

            if web_data.web_pages.value:
                '''
                If web pages are available, print the # of responses, and the first and second
                web pages returned.
                '''
                print("Webpage Results#{}".format(len(web_data.web_pages.value)))

                first_web_page = web_data.web_pages.value[0]
                print("First web page name: {} ".format(first_web_page.name))
                print("First web page URL: {} ".format(first_web_page.url))

            else:
                print("Didn't find any web pages...")

        except Exception as err:
            print("Encountered exception. {}".format(err))
    ```

1. Execute o programa.

### <a name="filter-for-news-and-freshness"></a>Filtrar por notícias e atualização

Este exemplo recorre aos parâmetros `response_filter` e `freshness` para filtrar os resultados da pesquisa com base no método [`search` do SDK](/python/api/azure-cognitiveservices-search-websearch/azure.cognitiveservices.search.websearch.operations.weboperations). Os resultados da pesquisa devolvidos estão limitados a artigos de notícias e páginas que o Bing descobriu nas últimas 24 horas. O `name` e `url` para o primeiro resultado são impressos.

1. Adicione este código ao seu projeto Python:

    ```python
    # Declare the function.
    def web_search_with_response_filter(subscription_key):
        client = WebSearchAPI(CognitiveServicesCredentials(subscription_key))
        try:
            '''
            Set the query, response_filter, and freshness using the SDK's search method. See:
            https://docs.microsoft.com/python/api/azure-cognitiveservices-search-websearch/azure.cognitiveservices.search.websearch.operations.weboperations?view=azure-python.
            '''
            web_data = client.web.search(query="xbox",
                response_filter=["News"],
                freshness="Day")
            print("\r\nSearching for \"xbox\" with the response filter set to \"News\" and freshness filter set to \"Day\".")

            '''
            If news articles are available, print the # of responses, and the first and second
            articles returned.
            '''
            if web_data.news.value:

                print("# of news results: {}".format(len(web_data.news.value)))

                first_web_page = web_data.news.value[0]
                print("First article name: {} ".format(first_web_page.name))
                print("First article URL: {} ".format(first_web_page.url))

                print("")

                second_web_page = web_data.news.value[1]
                print("\nSecond article name: {} ".format(second_web_page.name))
                print("Second article URL: {} ".format(second_web_page.url))

            else:
                print("Didn't find any news articles...")

        except Exception as err:
            print("Encountered exception. {}".format(err))

    # Call the function.
    web_search_with_response_filter(subscription_key)
    ```

1. Execute o programa.

### <a name="use-safe-search-answer-count-and-the-promote-filter"></a>Utilizar a pesquisa segura, a contagem de respostas e o filtro de promover

Este exemplo recorre aos parâmetros `answer_count`, `promote` e `safe_search` para filtrar os resultados da pesquisa com base no método [`search` do SDK](https://docs.microsoft.com/python/api/azure-cognitiveservices-search-websearch/azure.cognitiveservices.search.websearch.operations.weboperations?view=azure-python). O `name` e `url` para o primeiro resultado são apresentados.

1. Adicione este código ao seu projeto Python:

    ```python
    # Declare the function.
    def web_search_with_answer_count_promote_and_safe_search(subscription_key):

        client = WebSearchAPI(CognitiveServicesCredentials(subscription_key))

        try:
            '''
            Set the query, answer_count, promote, and safe_search parameters using the SDK's search method. See:
            https://docs.microsoft.com/python/api/azure-cognitiveservices-search-websearch/azure.cognitiveservices.search.websearch.operations.weboperations?view=azure-python.
            '''
            web_data = client.web.search(
                query="Niagara Falls",
                answer_count=2,
                promote=["videos"],
                safe_search=SafeSearch.strict  # or directly "Strict"
            )
            print("\r\nSearching for \"Niagara Falls\"")

            '''
            If results are available, print the # of responses, and the first result returned.
            '''
            if web_data.web_pages.value:

                print("Webpage Results#{}".format(len(web_data.web_pages.value)))

                first_web_page = web_data.web_pages.value[0]
                print("First web page name: {} ".format(first_web_page.name))
                print("First web page URL: {} ".format(first_web_page.url))

            else:
                print("Didn't see any Web data..")

        except Exception as err:
            print("Encountered exception. {}".format(err))
    ```

1. Execute o programa.

## <a name="clean-up-resources"></a>Limpar recursos

Quando tiver terminado este projeto, certifique-se de que remove a sua chave de subscrição do código do programa e desativa o seu ambiente virtual.

## <a name="next-steps"></a>Passos Seguintes

> [!div class="nextstepaction"]
> [Cognitive Services Python SDK samples](https://github.com/Azure-Samples/cognitive-services-python-sdk-samples) (Exemplos de SDK do Python nos Serviços Cognitivos)

## <a name="see-also"></a>Consulte também

* [Referência do SDK Python do Azure](https://docs.microsoft.com/python/api/overview/azure/cognitiveservices/websearch)
