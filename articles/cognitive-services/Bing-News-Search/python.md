---
title: 'Início rápido: Efetua uma pesquisa de notícias com Python - API de REST de pesquisa de notícias do Bing'
titlesuffix: Azure Cognitive Services
description: Utilize este guia de introdução para enviar um pedido para a API de REST do Search do Bing notícias com Python e receber uma resposta JSON.
services: cognitive-services
author: aahill
manager: nitinme
ms.service: cognitive-services
ms.subservice: bing-news-search
ms.topic: quickstart
ms.date: 1/10/2019
ms.author: aahi
ms.custom: seodec2018
ms.openlocfilehash: 7745db722d1862e5254c0e0fb07605799a8f7457
ms.sourcegitcommit: 5839af386c5a2ad46aaaeb90a13065ef94e61e74
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/19/2019
ms.locfileid: "58076722"
---
# <a name="quickstart-perform-a-news-search-using-python-and-the-bing-news-search-rest-api"></a>Início rápido: Efetua uma pesquisa de notícias com Python e a API de REST de pesquisa do Bing notícias

Utilize este guia de introdução para efetuar a primeira chamada para a API de pesquisa de notícias do Bing e receber uma resposta JSON. Esta aplicação simple de JavaScript envia uma consulta de pesquisa para a API e processa os resultados. Embora esse aplicativo é escrito em Python, a API é uma Web RESTful compatível com a maioria das linguagens de programação do serviço.

Pode executar este exemplo de código como um bloco de notas do Jupyter no [MyBinder](https://mybinder.org) clicando no lançamento associador de destaque: 

[![Binder](https://mybinder.org/badge.svg)](https://mybinder.org/v2/gh/Microsoft/cognitive-services-notebooks/master?filepath=BingNewsSearchAPI.ipynb)

O código-fonte para este exemplo também está disponível no [GitHub](https://github.com/Azure-Samples/cognitive-services-REST-api-samples/blob/master/python/Search/BingNewsSearchv7.py).

## <a name="prerequisites"></a>Pré-requisitos

[!INCLUDE [cognitive-services-bing-news-search-signup-requirements](../../../includes/cognitive-services-bing-news-search-signup-requirements.md)]

Consulte também [dos serviços cognitivos preços - API de pesquisa Bing](https://azure.microsoft.com/pricing/details/cognitive-services/search-api/).

## <a name="create-and-initialize-the-application"></a>Criar e inicializar a aplicação

1. Criar um novo ficheiro de Python no seu IDE ou editor favorito e importe o módulo de pedido. Crie variáveis para a chave de subscrição, o ponto final e um termo de pesquisa. Pode encontrar o ponto final no dashboard do Azure.

```python
import requests

subscription_key = "your subscription key"
search_term = "Microsoft"
search_url = "https://api.cognitive.microsoft.com/bing/v7.0/news/search"
```

### <a name="create-parameters-for-the-request"></a>Criar parâmetros para o pedido

1. Adicionar a chave de subscrição para um novo dicionário, usando `"Ocp-Apim-Subscription-Key"` como a chave. Fazer o mesmo para os parâmetros de pesquisa.

    ```python
    headers = {"Ocp-Apim-Subscription-Key" : subscription_key}
    params  = {"q": search_term, "textDecorations": True, "textFormat": "HTML"}
    ```

## <a name="send-a-request-and-get-a-response"></a>Enviar um pedido e obter uma resposta

1. Utilize a biblioteca de pedidos para chamar a API de pesquisa Visual do Bing com a chave de subscrição e os objetos de dicionário criados no último passo.

    ```python
    response = requests.get(search_url, headers=headers, params=params)
    response.raise_for_status()
    search_results = response.json()
    ```

2. `search_results` contém a resposta da API como um objeto JSON. Acessar as descrições de artigos contidos na resposta.
    
    ```python
    descriptions = [article["description"] for article in search_results["value"]]
    ```

## <a name="displaying-the-results"></a>Exibindo os resultados

Estas descrições, em seguida, podem ser compostas numa tabela com a palavra-chave de pesquisa realçada a **negrito**.

```python
from IPython.display import HTML
rows = "\n".join(["<tr><td>{0}</td></tr>".format(desc) for desc in descriptions])
HTML("<table>"+rows+"</table>")
```

## <a name="next-steps"></a>Passos Seguintes

> [!div class="nextstepaction"]
> [Criar uma aplicação Web de página única](tutorial-bing-news-search-single-page-app.md)
