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
ms.openlocfilehash: 51dcc0585117d2e3db97046a25a4fbae6cff0112
ms.sourcegitcommit: 2a8a53e5438596f99537f7279619258e9ecb357a
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 11/06/2020
ms.locfileid: "94340946"
---
Começa com a biblioteca de clientes Bing Custom Search para Python. Siga estes passos para instalar a embalagem e experimente o código de exemplo para tarefas básicas. A API de Pesquisa Personalizada Bing permite-lhe criar experiências de pesquisa personalizadas e sem anúncios para tópicos que lhe interessam. O código-fonte desta amostra pode ser encontrado no [GitHub](https://github.com/Azure-Samples/cognitive-services-python-sdk-samples/blob/master/samples/search/custom_search_samples.py).

Utilize a biblioteca de clientes Bing Custom Search para Python para:
* Encontre os resultados da pesquisa na web, a partir da sua instância de Pesquisa Personalizada Bing.

[Documentação de referência](/python/api/azure-cognitiveservices-search-customsearch/?view=azure-python)  |  [Código fonte da biblioteca](https://github.com/Azure/azure-sdk-for-python/tree/master/sdk/cognitiveservices/azure-cognitiveservices-search-customsearch)  |  [Pacote (PyPi)](https://pypi.org/project/azure-cognitiveservices-search-customsearch/)  |  [Amostras](https://github.com/Azure-Samples/cognitive-services-python-sdk-samples/)


## <a name="prerequisites"></a>Pré-requisitos

- Um caso de pesquisa personalizada Bing. Consulte [Quickstart: Crie a sua primeira instância de pesquisa personalizada Bing](../../quick-start.md) para obter mais informações.
- Python [2.x ou 3.x](https://www.python.org/) 
- O [Bing Custom Search SDK para Python](https://pypi.org/project/azure-cognitiveservices-search-customsearch/) 

[!INCLUDE [cognitive-services-bing-custom-search-prerequisites](~/includes/cognitive-services-bing-custom-search-signup-requirements.md)]

## <a name="install-the-python-client-library"></a>Instale a biblioteca do cliente Python

Instale a biblioteca do cliente Bing Custom Search com o seguinte comando.

```Console
python -m pip install azure-cognitiveservices-search-customsearch
```


## <a name="create-a-new-application"></a>Criar uma nova aplicação

Crie um novo ficheiro Python no seu editor favorito ou IDE e adicione as seguintes importações.

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

2. Crie um exemplo de `CustomSearchClient` , utilizando um objeto com a chave de `CognitiveServicesCredentials` subscrição. 

    ```python
    client = CustomSearchClient(endpoint=endpoint, credentials=CognitiveServicesCredentials(subscription_key))
    ```

3. Envie um pedido de pesquisa com `client.custom_instance.search()` . Anexar o seu termo de pesquisa ao `query` parâmetro e definir para o seu `custom_config` ID de configuração personalizado para usar o seu caso de pesquisa. Pode obter o seu ID a partir do [portal Bing Custom Search,](https://www.customsearch.ai/)clicando no separador **Produção.**

    ```python
    web_data = client.custom_instance.search(query="xbox", custom_config="your-configuration-id")
    ```

## <a name="view-the-search-results"></a>Ver os resultados da pesquisa

Se forem encontrados resultados de pesquisa de página web, obtenha o primeiro e imprima o seu nome, URL e páginas web totais encontradas.

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
> [Construa um aplicativo web de pesquisa personalizada](../../tutorials/custom-search-web-page.md)
