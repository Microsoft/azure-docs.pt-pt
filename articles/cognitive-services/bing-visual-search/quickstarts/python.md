---
title: 'Quickstart: Obtenha insights de imagem usando a API REST e Python - Bing Visual Search'
titleSuffix: Azure Cognitive Services
description: Aprenda a enviar uma imagem para a API de Pesquisa Visual Bing e obtenha informações sobre isso.
services: cognitive-services
author: swhite-msft
manager: nitinme
ms.service: cognitive-services
ms.subservice: bing-visual-search
ms.topic: quickstart
ms.date: 05/22/2020
ms.author: scottwhi
ms.custom: tracking-python
ms.openlocfilehash: 8a28b187898bc9725b79bfb73c76d161b5ecd4d5
ms.sourcegitcommit: 1de57529ab349341447d77a0717f6ced5335074e
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 06/09/2020
ms.locfileid: "84607266"
---
# <a name="quickstart-get-image-insights-using-the-bing-visual-search-rest-api-and-python"></a>Quickstart: Obtenha insights de imagem usando a API e Python Bing Visual Search REST

Utilize este quickstart para fazer a sua primeira chamada para a API de Pesquisa Visual Bing. Esta aplicação Python envia uma imagem para a API e exibe a informação que retorna. Embora esta aplicação esteja escrita em Python, a API é um serviço Web RESTful compatível com a maioria das linguagens de programação.

## <a name="prerequisites"></a>Pré-requisitos

* [Python 3.x](https://www.python.org/)

[!INCLUDE [cognitive-services-bing-visual-search-signup-requirements](../../../../includes/cognitive-services-bing-visual-search-signup-requirements.md)]

## <a name="initialize-the-application"></a>Inicializar a aplicação

1. Crie um novo ficheiro Python no seu IDE ou editor favorito e adicione a seguinte `import` declaração:

    ```python
    import requests, json
    ```

2. Crie variáveis para a sua chave de subscrição, ponto final e o caminho para a imagem que está a carregar. Para o valor de `BASE_URI` , pode utilizar o ponto final global no seguinte código, ou utilizar o ponto final personalizado [subdomínio](../../../cognitive-services/cognitive-services-custom-subdomains.md) apresentado no portal Azure para o seu recurso.

    ```python

    BASE_URI = 'https://api.cognitive.microsoft.com/bing/v7.0/images/visualsearch'
    SUBSCRIPTION_KEY = 'your-subscription-key'
    imagePath = 'your-image-path'
    ```
    
3. Ao carregar uma imagem local, os dados do formulário devem incluir o `Content-Disposition` cabeçalho. Desaveie o seu `name` parâmetro para "imagem", e desave o `filename` parâmetro para o nome do ficheiro da sua imagem. O conteúdo do formulário inclui os dados binários da imagem. O tamanho máximo de imagem que pode carregar é de 1 MB.
    
    ```
    --boundary_1234-abcd
    Content-Disposition: form-data; name="image"; filename="myimagefile.jpg"
    
    ÿØÿà JFIF ÖÆ68g-¤CWŸþ29ÌÄøÖ‘º«™æ±èuZiÀ)"óÓß°Î= ØJ9á+*G¦...
    
    --boundary_1234-abcd--
    ```

4. Crie um objeto de dicionário para guardar as informações do cabeçalho do seu pedido. Ligue a chave de subscrição à `Ocp-Apim-Subscription-Key` cadeia.

    ```python
    HEADERS = {'Ocp-Apim-Subscription-Key': SUBSCRIPTION_KEY}
    ```

5. Crie outro dicionário para conter a sua imagem, que é aberta e carregada quando envia o pedido.

    ```python
    file = {'image' : ('myfile', open(imagePath, 'rb'))}
    ```

## <a name="parse-the-json-response"></a>Parse a resposta JSON

Crie um método chamado `print_json()` para aceitar a resposta da API e imprimir o JSON.

```python
def print_json(obj):
    """Print the object as json"""
    print(json.dumps(obj, sort_keys=True, indent=2, separators=(',', ': ')))
```

## <a name="send-the-request"></a>Enviar o pedido

Utilize `requests.post()` para enviar um pedido à API de Pesquisa Visual Bing. Inclua o fio para o seu ponto final, cabeçalho e informações de arquivo. Imprimir `response.json()` com `print_json()` .

```python
try:
    response = requests.post(BASE_URI, headers=HEADERS, files=file)
    response.raise_for_status()
    print_json(response.json())
    
except Exception as ex:
    raise ex
```

## <a name="next-steps"></a>Próximos passos

> [!div class="nextstepaction"]
> [Criar uma aplicação web de pesquisa visual de uma página](../tutorial-bing-visual-search-single-page-app.md)
