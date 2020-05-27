---
title: 'Quickstart: Obtenha insights de imagem usando o REST API e Python - Bing Visual Search'
titleSuffix: Azure Cognitive Services
description: Aprenda a enviar uma imagem para a API de Pesquisa Visual bing e obtenha informações sobre isso.
services: cognitive-services
author: swhite-msft
manager: nitinme
ms.service: cognitive-services
ms.subservice: bing-visual-search
ms.topic: quickstart
ms.date: 05/22/2020
ms.author: scottwhi
ms.openlocfilehash: 7b33a857953b7f96180e306195dd0e8b21450556
ms.sourcegitcommit: 64fc70f6c145e14d605db0c2a0f407b72401f5eb
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 05/27/2020
ms.locfileid: "83874002"
---
# <a name="quickstart-get-image-insights-using-the-bing-visual-search-rest-api-and-python"></a>Quickstart: Obtenha insights de imagem usando a Bing Visual Search REST API e Python

Use este quickstart para fazer a sua primeira chamada para a API de Pesquisa Visual Bing. Esta aplicação Python envia uma imagem para a API e exibe a informação que devolve. Embora esta aplicação esteja escrita em Python, a API é um serviço Web RESTful compatível com a maioria dos idiomas de programação.

## <a name="prerequisites"></a>Pré-requisitos

* [Python 3.x](https://www.python.org/)

[!INCLUDE [cognitive-services-bing-visual-search-signup-requirements](../../../../includes/cognitive-services-bing-visual-search-signup-requirements.md)]

## <a name="initialize-the-application"></a>Inicializar a aplicação

1. Crie um novo ficheiro Python no seu IDE ou editor favorito e adicione a seguinte `import` declaração:

    ```python
    import requests, json
    ```

2. Crie variáveis para a sua chave de subscrição, ponto final e o caminho para a imagem que está a carregar. Pelo valor de , pode utilizar o ponto final global no seguinte código ou utilizar o ponto final de `BASE_URI` [subdomínio personalizado](../../../cognitive-services/cognitive-services-custom-subdomains.md) exibido no portal Azure para o seu recurso.

    ```python

    BASE_URI = 'https://api.cognitive.microsoft.com/bing/v7.0/images/visualsearch'
    SUBSCRIPTION_KEY = 'your-subscription-key'
    imagePath = 'your-image-path'
    ```
    
3. Ao fazer o upload de uma imagem local, os dados do formulário devem incluir o `Content-Disposition` cabeçalho. Defina o parâmetro `name` para "imagem", e coloque o `filename` parâmetro no nome de ficheiro da sua imagem. O conteúdo do formulário inclui os dados binários da imagem. O tamanho máximo de imagem que pode carregar é de 1 MB.
    
    ```
    --boundary_1234-abcd
    Content-Disposition: form-data; name="image"; filename="myimagefile.jpg"
    
    ÿØÿà JFIF ÖÆ68g-¤CWŸþ29ÌÄøÖ‘º«™æ±èuZiÀ)"óÓß°Î= ØJ9á+*G¦...
    
    --boundary_1234-abcd--
    ```

4. Crie um objeto de dicionário para guardar as informações do cabeçalho do seu pedido. Ligue a sua chave de subscrição à corda `Ocp-Apim-Subscription-Key` .

    ```python
    HEADERS = {'Ocp-Apim-Subscription-Key': SUBSCRIPTION_KEY}
    ```

5. Crie outro dicionário para conter a sua imagem, que é aberta e carregada quando envia o pedido.

    ```python
    file = {'image' : ('myfile', open(imagePath, 'rb'))}
    ```

## <a name="parse-the-json-response"></a>Parse a resposta JSON

Crie um método chamado `print_json()` a aceitar a resposta da API e imprima o JSON.

```python
def print_json(obj):
    """Print the object as json"""
    print(json.dumps(obj, sort_keys=True, indent=2, separators=(',', ': ')))
```

## <a name="send-the-request"></a>Enviar o pedido

Utilize para enviar um pedido para a API de `requests.post()` Pesquisa Visual Bing. Inclua a corda para o seu ponto final, cabeçalho e informações de arquivo. Imprimir `response.json()` com `print_json()` .

```python
try:
    response = requests.post(BASE_URI, headers=HEADERS, files=file)
    response.raise_for_status()
    print_json(response.json())
    
except Exception as ex:
    raise ex
```

## <a name="next-steps"></a>Passos seguintes

> [!div class="nextstepaction"]
> [Criar uma aplicação web de pesquisa visual de uma página](../tutorial-bing-visual-search-single-page-app.md)
