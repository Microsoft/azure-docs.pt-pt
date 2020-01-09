---
title: 'Início rápido: chamar seu ponto de extremidade de Pesquisa Personalizada do Bing usando o SDK do Python'
titleSuffix: Azure Cognitive Services
description: Use este guia de início rápido para começar a solicitar resultados de pesquisa de sua instância de Pesquisa Personalizada do Bing usando o SDK do Python.
services: cognitive-services
author: aahill
manager: nitinme
ms.service: cognitive-services
ms.subservice: bing-custom-search
ms.topic: quickstart
ms.date: 12/09/2019
ms.author: aahi
ms.openlocfilehash: ca0c1174abb80452f78766c3c4f49af73c790d3e
ms.sourcegitcommit: f4f626d6e92174086c530ed9bf3ccbe058639081
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 12/25/2019
ms.locfileid: "75448685"
---
# <a name="quickstart-call-your-bing-custom-search-endpoint-using-the-python-sdk"></a>Início rápido: chamar seu ponto de extremidade de Pesquisa Personalizada do Bing usando o SDK do Python 

Use este guia de início rápido para começar a solicitar resultados de pesquisa de sua instância do Pesquisa Personalizada do Bing, usando o SDK do Python. Embora Pesquisa Personalizada do Bing tenha uma API REST compatível com a maioria das linguagens de programação, o SDK do Pesquisa Personalizada do Bing fornece uma maneira fácil de integrar o serviço em seus aplicativos. O código-fonte para este exemplo pode ser encontrado no [GitHub](https://github.com/Azure-Samples/cognitive-services-python-sdk-samples/blob/master/samples/search/custom_search_samples.py) com mais anotações e tratamento de erros.

## <a name="prerequisites"></a>Pré-requisitos

- Uma instância de Pesquisa Personalizada do Bing. Consulte [início rápido: criar sua primeira instância de pesquisa personalizada do Bing](quick-start.md) para obter mais informações.
- Python [2. x ou 3. x](https://www.python.org/) 
- O [SDK do pesquisa personalizada do Bing para Python](https://pypi.org/project/azure-cognitiveservices-search-customsearch/) 

## <a name="install-the-python-sdk"></a>Instalar o SDK do Python

Instale o SDK do Pesquisa Personalizada do Bing com o comando a seguir.

```Console
python -m pip install azure-cognitiveservices-search-customsearch
```


## <a name="create-a-new-application"></a>Criar uma nova aplicação

Crie um novo arquivo Python em seu editor favorito ou IDE e adicione as importações a seguir.

```python
from azure.cognitiveservices.search.customsearch import CustomSearchClient
from msrest.authentication import CognitiveServicesCredentials
```

## <a name="create-a-search-client-and-send-a-request"></a>Criar um cliente de pesquisa e enviar uma solicitação

1. Crie uma variável para sua chave de assinatura.

    ```python
    subscription_key = 'your-subscription-key'
    ```

2. Crie uma instância do `CustomSearchClient`, usando um objeto `CognitiveServicesCredentials` com a chave de assinatura. 

    ```python
    client = CustomSearchClient(CognitiveServicesCredentials(subscription_key))
    ```

3. Envie uma solicitação de pesquisa com `client.custom_instance.search()`. Acrescente seu termo de pesquisa ao parâmetro `query` e defina `custom_config` como sua ID de configuração personalizada para usar sua instância de pesquisa. Você pode obter sua ID no [portal de pesquisa personalizada do Bing](https://www.customsearch.ai/), clicando na guia **produção** .

    ```python
    web_data = client.custom_instance.search(query="xbox", custom_config="your-configuration-id")
    ```

## <a name="view-the-search-results"></a>Exibir os resultados da pesquisa

Se algum resultado da pesquisa de página da Web for encontrado, obtenha o primeiro e imprima seu nome, URL e total de páginas da Web encontradas.

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
> [Criar um aplicativo Web de pesquisa personalizado](./tutorials/custom-search-web-page.md)
