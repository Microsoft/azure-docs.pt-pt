---
title: 'Quickstart: Realizar uma pesquisa de notícias com Python e a Bing News Search REST API'
titleSuffix: Azure Cognitive Services
description: Use este quickstart para enviar um pedido para a Bing News Search REST API usando Python, e receber uma resposta JSON.
services: cognitive-services
author: aahill
manager: nitinme
ms.service: cognitive-services
ms.subservice: bing-news-search
ms.topic: quickstart
ms.date: 12/12/2019
ms.author: aahi
ms.custom: seodec2018
ms.openlocfilehash: 1c424c75a4df193ec412355607c68abeda0560a5
ms.sourcegitcommit: 34a6fa5fc66b1cfdfbf8178ef5cdb151c97c721c
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/28/2020
ms.locfileid: "75448500"
---
# <a name="quickstart-perform-a-news-search-using-python-and-the-bing-news-search-rest-api"></a>Quickstart: Realizar uma pesquisa de notícias usando Python e a Bing News Search REST API

Use este quickstart para fazer a sua primeira chamada para a API de Pesquisa de Notícias Bing e receber uma resposta JSON. Esta simples aplicação JavaScript envia uma consulta de pesquisa para a API e processa os resultados. Embora esta aplicação esteja escrita em Python, a API é um serviço Web restful compatível com a maioria dos idiomas de programação.

Você pode executar esta amostra de código como um caderno Jupyter no [MyBinder](https://mybinder.org) clicando no crachá de lançamento Binder: 

[![Aglutinante](https://mybinder.org/badge.svg)](https://mybinder.org/v2/gh/Microsoft/cognitive-services-notebooks/master?filepath=BingNewsSearchAPI.ipynb)

O código fonte desta amostra também está disponível no [GitHub](https://github.com/Azure-Samples/cognitive-services-REST-api-samples/blob/master/python/Search/BingNewsSearchv7.py).

## <a name="prerequisites"></a>Pré-requisitos

[!INCLUDE [cognitive-services-bing-news-search-signup-requirements](../../../includes/cognitive-services-bing-news-search-signup-requirements.md)]

## <a name="create-and-initialize-the-application"></a>Criar e inicializar a aplicação

1. Crie um novo ficheiro Python no seu IDE ou editor favorito e importe o módulo de pedido. Crie variáveis para a sua chave de subscrição, ponto final e um termo de pesquisa. Pode utilizar o ponto final global abaixo, ou o ponto final personalizado do [subdomínio](../../cognitive-services/cognitive-services-custom-subdomains.md) exibido no portal Azure para o seu recurso.

```python
import requests

subscription_key = "your subscription key"
search_term = "Microsoft"
search_url = "https://api.cognitive.microsoft.com/bing/v7.0/news/search"
```

### <a name="create-parameters-for-the-request"></a>Criar parâmetros para o pedido

1. Adicione a sua chave de subscrição a um novo dicionário, utilizando `"Ocp-Apim-Subscription-Key"` como chave. Faça o mesmo pelos seus parâmetros de pesquisa.

    ```python
    headers = {"Ocp-Apim-Subscription-Key" : subscription_key}
    params  = {"q": search_term, "textDecorations": True, "textFormat": "HTML"}
    ```

## <a name="send-a-request-and-get-a-response"></a>Envie um pedido e obtenha uma resposta

1. Utilize a biblioteca de pedidos para ligar para a API de pesquisa visual bing usando a sua chave de subscrição, e os objetos dicionáriocriados criados no último passo.

    ```python
    response = requests.get(search_url, headers=headers, params=params)
    response.raise_for_status()
    search_results = response.json()
    ```

2. `search_results`contém a resposta da API como um objeto JSON. Aceda às descrições dos artigos contidos na resposta.
    
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

## <a name="next-steps"></a>Passos seguintes

> [!div class="nextstepaction"]
> [Criar uma aplicação web de página única](tutorial-bing-news-search-single-page-app.md)
