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
ms.date: 12/17/2019
ms.author: scottwhi
ms.openlocfilehash: b56f6743b642904349797ac5b6167194f7916b45
ms.sourcegitcommit: 9ee0cbaf3a67f9c7442b79f5ae2e97a4dfc8227b
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/27/2020
ms.locfileid: "75446595"
---
# <a name="quickstart-get-image-insights-using-the-bing-visual-search-rest-api-and-python"></a>Quickstart: Obtenha insights de imagem usando a Bing Visual Search REST API e Python

Use este quickstart para fazer a sua primeira chamada para a API de Pesquisa Visual Bing e veja os resultados. Esta aplicação Python envia uma imagem para a API e exibe a informação que devolve. Embora esta aplicação esteja escrita em Python, a API é um serviço Web RESTful compatível com a maioria dos idiomas de programação.

## <a name="prerequisites"></a>Pré-requisitos

* [Python 3.x](https://www.python.org/)

[!INCLUDE [cognitive-services-bing-visual-search-signup-requirements](../../../../includes/cognitive-services-bing-visual-search-signup-requirements.md)]

## <a name="initialize-the-application"></a>Inicializar a aplicação

1. Crie um novo ficheiro Python no seu IDE `import` ou editor favorito e adicione a seguinte declaração:

    ```python
    import requests, json
    ```

2. Crie variáveis para a sua chave de subscrição, ponto final e o caminho para a imagem que está a carregar. `BASE_URI`pode ser o ponto final global abaixo, ou o ponto final personalizado do [subdomínio](../../../cognitive-services/cognitive-services-custom-subdomains.md) exibido no portal Azure para o seu recurso:

    ```python

    BASE_URI = 'https://api.cognitive.microsoft.com/bing/v7.0/images/visualsearch'
    SUBSCRIPTION_KEY = 'your-subscription-key'
    imagePath = 'your-image-path'
    ```
    
    Ao fazer o upload de uma imagem `Content-Disposition` local, os dados do formulário devem incluir o cabeçalho. Deve definir `name` o parâmetro para "imagem", e `filename` pode definir o parâmetro em qualquer corda. O conteúdo do formulário inclui os dados binários da imagem. O tamanho máximo de imagem que pode carregar é de 1 MB.
    
    ```
    --boundary_1234-abcd
    Content-Disposition: form-data; name="image"; filename="myimagefile.jpg"
    
    ÿØÿà JFIF ÖÆ68g-¤CWŸþ29ÌÄøÖ‘º«™æ±èuZiÀ)"óÓß°Î= ØJ9á+*G¦...
    
    --boundary_1234-abcd--
    ```

3. Crie um objeto de dicionário para guardar as informações do cabeçalho do seu pedido. Ligue a sua chave `Ocp-Apim-Subscription-Key`de subscrição à corda, como mostrado abaixo:

    ```python
    HEADERS = {'Ocp-Apim-Subscription-Key': SUBSCRIPTION_KEY}
    ```

4. Crie outro dicionário para conter a sua imagem, que é aberta e carregada quando envia o pedido:

    ```python
    file = {'image' : ('myfile', open(imagePath, 'rb'))}
    ```

## <a name="parse-the-json-response"></a>Parse a resposta JSON

1. Criar um `print_json()` método chamado a acolher a resposta da API e imprimir o JSON:

    ```python
    def print_json(obj):
        """Print the object as json"""
        print(json.dumps(obj, sort_keys=True, indent=2, separators=(',', ': ')))
    ```

## <a name="send-the-request"></a>Enviar o pedido

1. Utilize `requests.post()` para enviar um pedido para a API de Pesquisa Visual Bing. Inclua a corda para o seu ponto final, cabeçalho e informações de arquivo. Imprimir `response.json()` `print_json()`com:

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
