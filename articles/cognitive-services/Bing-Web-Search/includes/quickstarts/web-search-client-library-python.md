---
title: Bing Web Search Python biblioteca de clientes quickstart
titleSuffix: Azure Cognitive Services
services: cognitive-services
author: aahill
manager: nitinme
ms.service: cognitive-services
ms.topic: include
ms.date: 03/05/2020
ms.author: aahi
ms.openlocfilehash: 13e08d2c273b242a902203abf0879fd11fbdf801
ms.sourcegitcommit: 8a1ba1ebc76635b643b6634cc64e137f74a1e4da
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 11/09/2020
ms.locfileid: "94386501"
---
A biblioteca de clientes Bing Web Search facilita a integração da Bing Web Search na sua aplicação Python. Neste início rápido, vai aprender como enviar um pedido, receber uma resposta JSON e filtrar e analisar os resultados.

Quer ver o código imediatamente? As amostras para as bibliotecas de [clientes Bing Search para Python](https://github.com/Azure-Samples/cognitive-services-python-sdk-samples/tree/master/samples/search) estão disponíveis no GitHub.


## <a name="prerequisites"></a>Pré-requisitos
O SDK de Pesquisa na Web do Bing é compatível com o Python 2.7, 3.3, 3.4, 3.5 e 3.6. Recomendamos utilizar um ambiente virtual para este início rápido.

* Python 2.7, 3.3, 3.4, 3.5 ou 3.6
* [virtualenv](https://docs.python.org/3/tutorial/venv.html) para Python 2.7
* [venv](https://pypi.python.org/pypi/virtualenv) para Python 3.x

[!INCLUDE [bing-web-search-quickstart-signup](~/includes/bing-web-search-quickstart-signup.md)]

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

Ativar o seu ambiente:

```console
mytestenv\Scripts\activate.bat
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

1. Copie este código de amostra no seu projeto. `endpoint` pode ser o ponto final global abaixo, ou o ponto final [personalizado subdomínio](~/articles/cognitive-services/cognitive-services-custom-subdomains.md) exibido no portal Azure para o seu recurso.:  

    ```python
    # Import required modules.
    from azure.cognitiveservices.search.websearch import WebSearchClient
    from azure.cognitiveservices.search.websearch.models import SafeSearch
    from msrest.authentication import CognitiveServicesCredentials

    # Replace with your subscription key.
    subscription_key = "YOUR_SUBSCRIPTION_KEY"

    # Instantiate the client and replace with your endpoint.
    client = WebSearchClient(endpoint="YOUR_ENDPOINT", credentials=CognitiveServicesCredentials(subscription_key))

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

1. Substitua `SUBSCRIPTION_KEY` por uma chave de subscrição válida.

1. `YOUR_ENDPOINT`Substitua-a pelo url do ponto final no portal e retire a secção "bing/v7.0" do ponto final.

1. Executar o programa. Por exemplo: `python your_program.py`.

## <a name="define-functions-and-filter-results"></a>Definir funções e filtrar resultados

Agora que fez a sua primeira chamada para a API de Pesquisa web de Bing, vamos ver algumas funções. As secções seguintes destacam a funcionalidade SDK para refinar consultas e resultados de filtragem. Cada função pode ser adicionada ao programa Python que criou na secção anterior.

### <a name="limit-the-number-of-results-returned-by-bing"></a>Limitar o número de resultados devolvidos pelo Bing

Esta amostra utiliza os `count` parâmetros e `offset` parâmetros para limitar o número de resultados devolvidos utilizando o [ `search` método](/python/api/azure-cognitiveservices-search-websearch/azure.cognitiveservices.search.websearch.operations.weboperations?view=azure-python)do SDK . O `name` e `url` para o primeiro resultado são impressos.

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

1. Executar o programa.

### <a name="filter-for-news-and-freshness"></a>Filtrar por notícias e atualização

Esta amostra utiliza os `response_filter` parâmetros e `freshness` parâmetros para filtrar os resultados da pesquisa utilizando o [ `search` método](/python/api/azure-cognitiveservices-search-websearch/azure.cognitiveservices.search.websearch.operations.weboperations)do SDK . Os resultados da pesquisa devolvidos estão limitados a artigos de notícias e páginas que o Bing descobriu nas últimas 24 horas. O `name` e `url` para o primeiro resultado são impressos.

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

1. Executar o programa.

### <a name="use-safe-search-answer-count-and-the-promote-filter"></a>Utilizar a pesquisa segura, a contagem de respostas e o filtro de promover

Esta amostra utiliza os `answer_count` `promote` , e `safe_search` parâmetros para filtrar os resultados de pesquisa utilizando o [ `search` método](/python/api/azure-cognitiveservices-search-websearch/azure.cognitiveservices.search.websearch.operations.weboperations?view=azure-python)do SDK . O `name` e `url` para o primeiro resultado são apresentados.

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

1. Executar o programa.

## <a name="clean-up-resources"></a>Limpar recursos

Quando tiver terminado este projeto, certifique-se de que remove a sua chave de subscrição do código do programa e desativa o seu ambiente virtual.

## <a name="next-steps"></a>Passos seguintes

> [!div class="nextstepaction"]
> [Exemplos de SDK do Python nos Serviços Cognitivos](https://github.com/Azure-Samples/cognitive-services-python-sdk-samples)

## <a name="see-also"></a>Ver também

* [Referência do SDK Python do Azure](/python/api/azure-cognitiveservices-search-websearch/?view=azure-python)
