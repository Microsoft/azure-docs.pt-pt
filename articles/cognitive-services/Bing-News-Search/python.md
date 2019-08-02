---
title: 'Início rápido: Executar uma pesquisa de notícias com o Python e a API REST do Pesquisa de Notícias do Bing'
titleSuffix: Azure Cognitive Services
description: Use este guia de início rápido para enviar uma solicitação para a API REST do Pesquisa de Notícias do Bing usando o Python e receba uma resposta JSON.
services: cognitive-services
author: aahill
manager: nitinme
ms.service: cognitive-services
ms.subservice: bing-news-search
ms.topic: quickstart
ms.date: 6/18/2019
ms.author: aahi
ms.custom: seodec2018
ms.openlocfilehash: 2fc67cbfedccd69ff9063b0575ae51197eceb09d
ms.sourcegitcommit: 198c3a585dd2d6f6809a1a25b9a732c0ad4a704f
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 07/23/2019
ms.locfileid: "68423694"
---
# <a name="quickstart-perform-a-news-search-using-python-and-the-bing-news-search-rest-api"></a>Início rápido: Executar uma pesquisa de notícias usando o Python e a API REST do Pesquisa de Notícias do Bing

Use este guia de início rápido para fazer sua primeira chamada para o API de Pesquisa de Notícias do Bing e receber uma resposta JSON. Esse aplicativo JavaScript simples envia uma consulta de pesquisa para a API e processa os resultados. Embora esse aplicativo seja escrito em Python, a API é uma maioria das linguagens de programação compatíveis com o serviço Web RESTful.

Você pode executar este exemplo de código como um Jupyter Notebook [](https://mybinder.org) em mybinder clicando na notificação de lançamento do fichário: 

[![Binder](https://mybinder.org/badge.svg)](https://mybinder.org/v2/gh/Microsoft/cognitive-services-notebooks/master?filepath=BingNewsSearchAPI.ipynb)

O código-fonte para este exemplo também está disponível no [GitHub](https://github.com/Azure-Samples/cognitive-services-REST-api-samples/blob/master/python/Search/BingNewsSearchv7.py).

## <a name="prerequisites"></a>Pré-requisitos

[!INCLUDE [cognitive-services-bing-news-search-signup-requirements](../../../includes/cognitive-services-bing-news-search-signup-requirements.md)]

Consulte também [dos serviços cognitivos preços - API de pesquisa Bing](https://azure.microsoft.com/pricing/details/cognitive-services/search-api/).

## <a name="create-and-initialize-the-application"></a>Criar e inicializar a aplicação

1. Crie um novo arquivo Python em seu IDE ou editor favorito e importe o módulo de solicitação. Crie variáveis para sua chave de assinatura, ponto de extremidade e um termo de pesquisa. Você pode encontrar seu ponto de extremidade no painel do Azure.

```python
import requests

subscription_key = "your subscription key"
search_term = "Microsoft"
search_url = "https://api.cognitive.microsoft.com/bing/v7.0/news/search"
```

### <a name="create-parameters-for-the-request"></a>Criar parâmetros para a solicitação

1. Adicione sua chave de assinatura a um novo dicionário, `"Ocp-Apim-Subscription-Key"` usando como a chave. Faça o mesmo para os parâmetros de pesquisa.

    ```python
    headers = {"Ocp-Apim-Subscription-Key" : subscription_key}
    params  = {"q": search_term, "textDecorations": True, "textFormat": "HTML"}
    ```

## <a name="send-a-request-and-get-a-response"></a>Enviar uma solicitação e obter uma resposta

1. Use a biblioteca de solicitações para chamar o API da Pesquisa Visual do Bing usando sua chave de assinatura e os objetos de dicionário criados na última etapa.

    ```python
    response = requests.get(search_url, headers=headers, params=params)
    response.raise_for_status()
    search_results = response.json()
    ```

2. `search_results`contém a resposta da API como um objeto JSON. Acesse as descrições dos artigos contidos na resposta.
    
    ```python
    descriptions = [article["description"] for article in search_results["value"]]
    ```

## <a name="displaying-the-results"></a>Exibindo os resultados

Estas descrições, em seguida, podem ser compostas numa tabela com a palavra-chave de pesquisa realçada a **negrito**.

```python
from IPython.display import HTML
rows = "\n".join(["<tr><td>{0}</td></tr>".format(desc)
                  for desc in descriptions])
HTML("<table>"+rows+"</table>")
```

## <a name="next-steps"></a>Passos Seguintes

> [!div class="nextstepaction"]
> [Criar uma aplicação Web de página única](tutorial-bing-news-search-single-page-app.md)
