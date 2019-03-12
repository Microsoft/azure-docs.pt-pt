---
title: 'Início rápido: Chamar o ponto final de pesquisa personalizada do Bing com o SDK de Python | Documentos da Microsoft'
titleSuffix: Azure Cognitive Services
description: Utilize o SDK de pesquisa personalizada do Bing para Python para obter resultados de pesquisa personalizada.
services: cognitive-services
author: aahill
manager: nitinme
ms.service: cognitive-services
ms.subservice: bing-custom-search
ms.topic: quickstart
ms.date: 03/05/2019
ms.author: aahi
ms.openlocfilehash: c4c5059bc57ea33357145f6b119456dc6c5bdb7b
ms.sourcegitcommit: dd1a9f38c69954f15ff5c166e456fda37ae1cdf2
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/07/2019
ms.locfileid: "57571895"
---
# <a name="quickstart-call-your-bing-custom-search-endpoint-using-the-python-sdk"></a>Início rápido: Chamar o ponto final de pesquisa personalizada do Bing através do SDK Python 

Utilize este guia de introdução para começar a pedir os resultados da pesquisa da sua instância de pesquisa personalizada do Bing, utilizando o SDK de Python. Embora a pesquisa personalizada do Bing tenha uma API de REST compatível com a maioria das linguagens de programação, o SDK de pesquisa personalizada do Bing fornece uma forma fácil de integrar o serviço aos seus aplicativos. O código-fonte para este exemplo pode ser encontrado no [GitHub](https://github.com/Azure-Samples/cognitive-services-python-sdk-samples/blob/master/samples/search/custom_search_samples.py) com tratamento de erros adicionais e anotações.

## <a name="prerequisites"></a>Pré-requisitos

- Uma instância de pesquisa personalizada do Bing. Consulte [início rápido: Criar a primeira instância de pesquisa personalizada do Bing](quick-start.md) para obter mais informações.
- Python [2.x ou 3.x](https://www.python.org/) 
- O [pesquisa personalizada do Bing SDK para Python](https://pypi.org/project/azure-cognitiveservices-search-customsearch/) 

## <a name="install-the-python-sdk"></a>Instalar o SDK Python

Instale o SDK de pesquisa personalizada do Bing com o seguinte comando.

```Console
python -m pip install azure-cognitiveservices-search-customsearch
```


## <a name="create-a-new-application"></a>Criar uma nova aplicação

Crie um novo ficheiro de Python no seu editor favorito ou IDE e adicione as seguintes importações.

```python
from azure.cognitiveservices.search.customsearch import CustomSearchClient
from msrest.authentication import CognitiveServicesCredentials
```

## <a name="create-a-search-client-and-send-a-request"></a>Criar um cliente de pesquisa e enviar um pedido

1. Crie uma variável para a sua chave de subscrição.

    ```python
    subscription_key = 'your-subscription-key'
    ```

2. Criar uma instância do `CustomSearchClient`, utilizando um `CognitiveServicesCredentials` objeto com a chave de subscrição. 

    ```python
    client = CustomSearchClient(CognitiveServicesCredentials(subscription_key))
    ```

3. Enviar um pedido de pesquisa com `client.custom_instance.search()`. Acrescentar o termo de pesquisa para o `query` parâmetro e o conjunto `custom_config` ao seu ID de configuração personalizada para utilizar a sua instância de pesquisa. Pode obter sua ID do [portal de pesquisa personalizada do Bing](https://www.customsearch.ai/), clicando a **produção** separador.

    ```python
    web_data = client.custom_instance.search(query="xbox", custom_config="your-configuration-id")
    ```

## <a name="view-the-search-results"></a>Ver os resultados da pesquisa

Se foram encontrados quaisquer resultados de pesquisa de página da web, obtenha primeiro e imprimir o seu nome, URL e total de páginas web foi encontrado.

```python
if web_data.web_pages.value:
    first_web_result = web_data.web_pages.value[0]
    print("Web Pages result count: {}".format(len(web_data.web_pages.value)))
    print("First Web Page name: {}".format(first_web_result.name))
    print("First Web Page url: {}".format(first_web_result.url))
else:
    print("Didn't see any web data..")
```

## <a name="next-steps"></a>Passos Seguintes

> [!div class="nextstepaction"]
> [Criar uma aplicação web de pesquisa personalizada](./tutorials/custom-search-web-page.md)
