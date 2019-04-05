---
title: 'Início rápido: Obtenha informações de imagem usando a API de REST de pesquisa Visual do Bing e Python'
titleSuffix: Azure Cognitive Services
description: Aprenda a carregar uma imagem para a API de pesquisa Visual do Bing e obter conhecimentos sobre ele.
services: cognitive-services
author: swhite-msft
manager: nitinme
ms.service: cognitive-services
ms.subservice: bing-visual-search
ms.topic: quickstart
ms.date: 4/02/2019
ms.author: scottwhi
ms.openlocfilehash: 7ec37b4c3bdeb924b3e35dbcb5d07a478611f631
ms.sourcegitcommit: 8313d5bf28fb32e8531cdd4a3054065fa7315bfd
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/05/2019
ms.locfileid: "59047131"
---
# <a name="quickstart-get-image-insights-using-the-bing-visual-search-rest-api-and-python"></a>Início rápido: Obtenha informações de imagem usando a API de REST de pesquisa Visual do Bing e Python

Utilize este guia de introdução para tornar a sua primeira chamada à API de pesquisa Visual do Bing e ver os resultados. Esta aplicação de Python carrega uma imagem para a API e apresenta as informações que ele retorna. Embora esse aplicativo é escrito em Python, a API é um serviço RESTful Web compatível com a maioria das linguagens de programação.

Ao carregar uma imagem do local, os dados do formulário tem de incluir o `Content-Disposition` cabeçalho. Tem de definir seu `name` parâmetro para "imagem" e pode definir o `filename` parâmetro para qualquer cadeia de caracteres. O conteúdo do formulário inclui os dados binários da imagem. O tamanho da imagem máximo que pode carregar é de 1 MB.

```
--boundary_1234-abcd
Content-Disposition: form-data; name="image"; filename="myimagefile.jpg"

ÿØÿà JFIF ÖÆ68g-¤CWŸþ29ÌÄøÖ‘º«™æ±èuZiÀ)"óÓß°Î= ØJ9á+*G¦...

--boundary_1234-abcd--
```

## <a name="prerequisites"></a>Pré-requisitos

* [Python 3.x](https://www.python.org/)

[!INCLUDE [cognitive-services-bing-visual-search-signup-requirements](../../../../includes/cognitive-services-bing-image-search-signup-requirements.md)]

## <a name="initialize-the-application"></a>Inicializar o aplicativo

1. Criar um novo ficheiro de Python no seu IDE ou editor favorito e adicione o seguinte `import` instrução:

    ```python
    import requests, json
    ```

2. Crie variáveis para a chave de subscrição, o ponto final e o caminho para a imagem que está a carregar:

    ```python

    BASE_URI = 'https://api.cognitive.microsoft.com/bing/v7.0/images/visualsearch'
    SUBSCRIPTION_KEY = 'your-subscription-key'
    imagePath = 'your-image-path'
    ```

3. Crie um objeto de dicionário para armazenar informações de cabeçalho do seu pedido. Ligar a sua chave de subscrição para a cadeia de caracteres `Ocp-Apim-Subscription-Key`, conforme mostrado abaixo:

    ```python
    HEADERS = {'Ocp-Apim-Subscription-Key': SUBSCRIPTION_KEY}
    ```

4. Crie outra dicionário para conter a sua imagem, que é aberta e carregada quando envia o pedido:

    ```python
    file = {'image' : ('myfile', open(imagePath, 'rb'))}
    ```

## <a name="parse-the-json-response"></a>Analisar a resposta JSON

1. Criar um método chamado `print_json()` tirar na resposta de API e imprimir o JSON:

    ```python
    def print_json(obj):
        """Print the object as json"""
        print(json.dumps(obj, sort_keys=True, indent=2, separators=(',', ': ')))
    ```

## <a name="send-the-request"></a>Enviar o pedido

1. Utilize `requests.post()` para enviar um pedido para a API de pesquisa Visual do Bing. Inclua a cadeia de caracteres para o seu ponto final, cabeçalho e informações de ficheiro. Impressão `response.json()` com `print_json()`:

    ```python
    try:
        response = requests.post(BASE_URI, headers=HEADERS, files=file)
        response.raise_for_status()
        print_json(response.json())
    
    except Exception as ex:
        raise ex
    ```

## <a name="next-steps"></a>Passos Seguintes

> [!div class="nextstepaction"]
> [Criar uma aplicação de web de página única de pesquisa Visual](../tutorial-bing-visual-search-single-page-app.md)
