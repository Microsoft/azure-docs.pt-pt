---
title: Bing Custom Search Python biblioteca de clientes quickstart
titleSuffix: Azure Cognitive Services
services: cognitive-services
author: aahill
manager: nitinme
ms.service: cognitive-services
ms.topic: include
ms.date: 02/27/2020
ms.author: aahi
ms.openlocfilehash: ec0ffdcf86e67a7126a3100c1e20b6e5c3474e35
ms.sourcegitcommit: e4c33439642cf05682af7f28db1dbdb5cf273cc6
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/03/2020
ms.locfileid: "78252885"
---
Começa com a biblioteca de clientes bing Custom Search para python. Siga estes passos para instalar a embalagem e experimente o código de exemplo para tarefas básicas. A API de Pesquisa Personalizada Bing permite-lhe criar experiências de pesquisa personalizadas e sem anúncios para tópicos com os seus interesses. O código fonte desta amostra pode ser encontrado no [GitHub](https://github.com/Azure-Samples/cognitive-services-python-sdk-samples/blob/master/samples/search/custom_search_samples.py).

Utilize a biblioteca de clientes Bing Custom Search para Python para:
* Encontre os resultados da pesquisa na web, a partir da sua instância de Pesquisa Personalizada Bing.

[Documentação de referência](https://docs.microsoft.com/python/api/overview/azure/cognitiveservices/customsearch?view=azure-python) | [código fonte | ](https://github.com/Azure/azure-sdk-for-python/tree/master/sdk/cognitiveservices/azure-cognitiveservices-search-customsearch) Pacote [(PyPi)](https://pypi.org/project/azure-cognitiveservices-search-customsearch/) | [Amostras](https://github.com/Azure-Samples/cognitive-services-python-sdk-samples/)


## <a name="prerequisites"></a>Pré-requisitos

- Uma instância de pesquisa personalizada bing. Consulte [Quickstart: Crie a sua primeira instância](../../quick-start.md) de Pesquisa Personalizada Bing para mais informações.
- Python [2.x ou 3.x](https://www.python.org/) 
- O [Bing Custom Search SDK para Python](https://pypi.org/project/azure-cognitiveservices-search-customsearch/) 

[!INCLUDE [cognitive-services-bing-custom-search-prerequisites](~/includes/cognitive-services-bing-custom-search-signup-requirements.md)]

## <a name="install-the-python-client-library"></a>Instale a biblioteca de clientes Python

Instale a biblioteca do cliente Bing Custom Search com o seguinte comando.

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

1. Crie uma variável para a sua chave de subscrição e ponto final.

    ```python
    subscription_key = 'your-subscription-key'
    endpoint = 'your-endpoint'
    ```

2. Crie uma instância de `CustomSearchClient`, utilizando um objeto `CognitiveServicesCredentials` com a chave de subscrição. 

    ```python
    client = CustomSearchClient(endpoint=endpoint, credentials=CognitiveServicesCredentials(subscription_key))
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
> [Construa uma aplicação web de pesquisa personalizada](../../tutorials/custom-search-web-page.md)
