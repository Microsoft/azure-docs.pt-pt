---
title: 'Quickstart: Ligue para o seu ponto final de pesquisa personalizada Bing usando o Python SDK'
titleSuffix: Azure Cognitive Services
description: Utilize este quickstart para começar a solicitar resultados de pesquisa da sua instância de pesquisa personalizada Bing utilizando o Python SDK.
services: cognitive-services
author: aahill
manager: nitinme
ms.service: cognitive-services
ms.subservice: bing-custom-search
ms.topic: quickstart
ms.date: 12/09/2019
ms.author: aahi
ms.openlocfilehash: d67075fad719b1780682c705f0e17f15c5801559
ms.sourcegitcommit: b95983c3735233d2163ef2a81d19a67376bfaf15
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 02/11/2020
ms.locfileid: "77136157"
---
# <a name="quickstart-call-your-bing-custom-search-endpoint-using-the-python-sdk"></a>Quickstart: Ligue para o seu ponto final de pesquisa personalizada Bing usando o Python SDK 

Utilize este quickstart para começar a solicitar resultados de pesquisa da sua instância de Pesquisa Personalizada Bing, utilizando o Python SDK. Embora o Bing Custom Search tenha um Rest API compatível com a maioria dos idiomas de programação, o Bing Custom Search SDK fornece uma maneira fácil de integrar o serviço nas suas aplicações. O código fonte desta amostra pode ser encontrado no [GitHub](https://github.com/Azure-Samples/cognitive-services-python-sdk-samples/blob/master/samples/search/custom_search_samples.py) com manipulação adicional de erros e anotações.

## <a name="prerequisites"></a>Pré-requisitos

- Uma instância de pesquisa personalizada bing. Consulte [Quickstart: Crie a sua primeira instância](quick-start.md) de Pesquisa Personalizada Bing para mais informações.
- Python [2.x ou 3.x](https://www.python.org/) 
- O [Bing Custom Search SDK para Python](https://pypi.org/project/azure-cognitiveservices-search-customsearch/) 

## <a name="install-the-python-sdk"></a>Instale o Python SDK

Instale o Bing Custom Search SDK com o seguinte comando.

```Console
python -m pip install azure-cognitiveservices-search-customsearch
```


## <a name="create-a-new-application"></a>Criar uma nova aplicação

Crie um novo ficheiro Python no seu editor favorito ou IDE, e adicione as seguintes importações.

```python
from azure.cognitiveservices.search.customsearch import CustomSearchClient
from msrest.authentication import CognitiveServicesCredentials
```

## <a name="create-a-search-client-and-send-a-request"></a>Criar um cliente de pesquisa e enviar um pedido

1. Crie uma variável para a sua chave de subscrição.

    ```python
    subscription_key = 'your-subscription-key'
    endpoint = 'your-custom-endpoint'
    ```

2. Crie uma instância de `CustomSearchClient`, utilizando um objeto `CognitiveServicesCredentials` com a chave de subscrição. 

    ```python
    client = CustomSearchClient(endpoint, CognitiveServicesCredentials(subscription_key))
    ```

3. Envie um pedido de pesquisa com `client.custom_instance.search()`. Acomode o seu termo de pesquisa ao parâmetro `query` e defina `custom_config` para o seu ID de Configuração Personalizada para utilizar a sua instância de pesquisa. Pode obter o seu ID no [portal bing Custom Search,](https://www.customsearch.ai/)clicando no separador **Produção.**

    ```python
    web_data = client.custom_instance.search(query="xbox", custom_config="your-configuration-id")
    ```

## <a name="view-the-search-results"></a>Ver os resultados da pesquisa

Se forem encontrados resultados de pesquisa de páginas web, obtenha o primeiro e imprima o seu nome, URL e páginas web totais encontradas.

```python
if web_data.web_pages.value:
    first_web_result = web_data.web_pages.value[0]
    print("Web Pages result count: {}".format(len(web_data.web_pages.value)))
    print("First Web Page name: {}".format(first_web_result.name))
    print("First Web Page url: {}".format(first_web_result.url))
else:
    print("Didn't see any web data..")
```

## <a name="next-steps"></a>Passos seguintes

> [!div class="nextstepaction"]
> [Construa uma aplicação web de pesquisa personalizada](./tutorials/custom-search-web-page.md)
