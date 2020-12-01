---
title: 'Quickstart: Realize uma pesquisa de notícias com Python e a Bing News Search REST API'
titleSuffix: Azure Cognitive Services
description: Utilize este quickstart para enviar um pedido para a Bing News Search REST API usando Python, e receba uma resposta JSON.
services: cognitive-services
author: aahill
manager: nitinme
ms.service: cognitive-services
ms.subservice: bing-news-search
ms.topic: quickstart
ms.date: 06/16/2020
ms.author: aahi
ms.custom: seodec2018, devx-track-python
ms.openlocfilehash: f47c0c5ad271b89348fe0baa8ac1fd5ebd9cf2eb
ms.sourcegitcommit: 9eda79ea41c60d58a4ceab63d424d6866b38b82d
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 11/30/2020
ms.locfileid: "96351270"
---
# <a name="quickstart-perform-a-news-search-using-python-and-the-bing-news-search-rest-api"></a>Quickstart: Realize uma pesquisa de notícias usando Python e a Bing News Search REST API

> [!WARNING]
> As APIs de Pesquisa de Bing estão a mover-se dos Serviços Cognitivos para os Serviços de Pesquisa Bing. A partir **de 30 de outubro de 2020,** quaisquer novos casos de Bing Search devem ser adquir-se na sequência do processo [aqui](/bing/search-apis/bing-web-search/create-bing-search-service-resource)documentado.
> Bing Search APIs aforados usando Serviços Cognitivos será suportado durante os próximos três anos ou até o final do seu Contrato de Empresa, o que acontecer primeiro.
> Para obter instruções de migração, consulte [os Serviços de Busca Bing.](/bing/search-apis/bing-web-search/create-bing-search-service-resource)

Use este quickstart para fazer a sua primeira chamada para a API de Pesquisa de Notícias Bing. Esta simples aplicação Python envia uma consulta de pesquisa para a API e processa o resultado do JSON. 

Embora esta aplicação esteja escrita em Python, a API é um serviço Web RESTful compatível com a maioria das linguagens de programação.

Para executar esta amostra de código como um caderno Jupyter no [MyBinder,](https://mybinder.org)selecione o crachá **de lançamento:** 

[![aglutinante de lançamento](https://mybinder.org/badge.svg)](https://mybinder.org/v2/gh/Microsoft/cognitive-services-notebooks/master?filepath=BingNewsSearchAPI.ipynb)

O código-fonte desta amostra também está disponível no [GitHub](https://github.com/Azure-Samples/cognitive-services-REST-api-samples/blob/master/python/Search/BingNewsSearchv7.py).

[!INCLUDE [cognitive-services-bing-news-search-signup-requirements](../../../includes/cognitive-services-bing-news-search-signup-requirements.md)]

## <a name="create-and-initialize-the-application"></a>Criar e inicializar a aplicação

Crie um novo ficheiro Python no seu IDE ou editor favorito e importe o módulo de pedido. Crie variáveis para a sua chave de subscrição, ponto final e termo de pesquisa. Pode utilizar o ponto final global no seguinte código ou utilizar o ponto final [de subdomínio personalizado](../../cognitive-services/cognitive-services-custom-subdomains.md) apresentado no portal Azure para o seu recurso.

```python
import requests

subscription_key = "your subscription key"
search_term = "Microsoft"
search_url = "https://api.cognitive.microsoft.com/bing/v7.0/news/search"
```

## <a name="create-parameters-for-the-request"></a>Criar parâmetros para o pedido

Adicione a chave de subscrição a um novo dicionário, utilizando `Ocp-Apim-Subscription-Key` como chave. Faça o mesmo pelos seus parâmetros de pesquisa.

```python
headers = {"Ocp-Apim-Subscription-Key" : subscription_key}
params  = {"q": search_term, "textDecorations": True, "textFormat": "HTML"}
```

## <a name="send-a-request-and-get-a-response"></a>Envie um pedido e obtenha uma resposta

1. Utilize a biblioteca de pedidos para ligar para a API de Pesquisa Visual Bing com a sua chave de subscrição e os objetos do dicionário que criou no passo anterior.

    ```python
    response = requests.get(search_url, headers=headers, params=params)
    response.raise_for_status()
    search_results = json.dumps(response.json())
    ```

2. Aceda às descrições dos artigos contidos na resposta da API, que é armazenada `search_results` como um objeto JSON. 
    
    ```python
    descriptions = [article["description"] for article in search_results["value"]]
    ```

## <a name="display-the-results"></a>Mostrar os resultados

Estas descrições, em seguida, podem ser compostas numa tabela com a palavra-chave de pesquisa realçada a negrito.

```python
from IPython.display import HTML
rows = "\n".join(["<tr><td>{0}</td></tr>".format(desc)
                  for desc in descriptions])
HTML("<table>"+rows+"</table>")
```

## <a name="next-steps"></a>Passos seguintes

> [!div class="nextstepaction"]
> [Criar uma aplicação web de página única](tutorial-bing-news-search-single-page-app.md)