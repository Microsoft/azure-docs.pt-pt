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
ms.date: 5/16/2018
ms.author: scottwhi
ms.openlocfilehash: 873da64592c2c2e925d8731d4b1154db95bed31d
ms.sourcegitcommit: 90cec6cccf303ad4767a343ce00befba020a10f6
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 02/07/2019
ms.locfileid: "55863232"
---
# <a name="quickstart-your-first-bing-visual-search-query-in-python"></a>Início rápido: Sua primeira consulta de pesquisa Visual do Bing em Python

Utilize este guia de introdução para efetuar a primeira chamada à API de pesquisa Visual do Bing e ver os resultados da pesquisa. Esta aplicação JavaScript simple carrega uma imagem para a API e apresenta as informações devolvidas sobre ele. Embora esse aplicativo é escrito em JavaScript, a API é um serviço RESTful Web compatível com a maioria das linguagens de programação.

Ao carregar uma imagem do local, os dados de formulário POST tem de incluir o cabeçalho Content-Disposition. O respetivo parâmetro `name` tem de ser definido como “image” e o parâmetro `filename` pode ser definido como qualquer cadeia. Os conteúdos do formulário são o binário da imagem. O tamanho máximo de imagem que pode carregar é 1 MB.

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

1. Criar um novo ficheiro de Python no seu IDE ou editor favorito e adicione a seguinte instrução de importação.

    ```python
    import requests, json
    ```

2. Crie variáveis para a chave de subscrição, o ponto final e o caminho para a imagem que está a carregar.

    ```python

    BASE_URI = 'https://api.cognitive.microsoft.com/bing/v7.0/images/visualsearch'
    SUBSCRIPTION_KEY = 'your-subscription-key'
    imagePath = 'your-image-path'
    ```

3. Crie um objeto de dicionário para armazenar informações de cabeçalho dos seus pedidos. Ligar a sua chave de subscrição para a cadeia de caracteres `Ocp-Apim-Subscription-Key`, conforme mostrado abaixo.

    ```python
    HEADERS = {'Ocp-Apim-Subscription-Key': SUBSCRIPTION_KEY}
    ```

4. Crie outra dicionário para conter a sua imagem, que será aberta e carregada quando envia o pedido. 

    ```python
    file = {'image' : ('myfile', open(imagePath, 'rb'))}
    ```

## <a name="parse-the-json-response"></a>Analisar a resposta JSON

1. Criar um método chamado `print_json()` tirar na resposta de API e imprimir o JSON.

    ```python
    def print_json(obj):
        """Print the object as json"""
        print(json.dumps(obj, sort_keys=True, indent=2, separators=(',', ': ')))
    ```

## <a name="send-the-request"></a>Enviar o pedido

1. Utilize `requests.post()` para enviar um pedido para a API de pesquisa Visual do Bing. Inclua a cadeia de caracteres para o seu ponto final, cabeçalho e informações de ficheiro. Impressão `response.json()` com `print_json()`

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
> [Criar uma aplicação web de pesquisa personalizada](../tutorial-bing-visual-search-single-page-app.md)
