---
title: 'Início rápido: obter informações de imagem usando a API REST e o Python-Pesquisa Visual do Bing'
titleSuffix: Azure Cognitive Services
description: Saiba como carregar uma imagem no API da Pesquisa Visual do Bing e obter informações sobre ela.
services: cognitive-services
author: swhite-msft
manager: nitinme
ms.service: cognitive-services
ms.subservice: bing-visual-search
ms.topic: quickstart
ms.date: 12/17/2019
ms.author: scottwhi
ms.openlocfilehash: b56f6743b642904349797ac5b6167194f7916b45
ms.sourcegitcommit: f4f626d6e92174086c530ed9bf3ccbe058639081
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 12/25/2019
ms.locfileid: "75446595"
---
# <a name="quickstart-get-image-insights-using-the-bing-visual-search-rest-api-and-python"></a>Início rápido: obter informações de imagem usando a API REST do Pesquisa Visual do Bing e o Python

Use este guia de início rápido para fazer sua primeira chamada para a API da Pesquisa Visual do Bing e exibir os resultados. Esse aplicativo Python carrega uma imagem na API e exibe as informações que ele retorna. Embora esse aplicativo seja escrito em Python, a API é um serviço Web RESTful compatível com a maioria das linguagens de programação.

## <a name="prerequisites"></a>Pré-requisitos

* [Python 3. x](https://www.python.org/)

[!INCLUDE [cognitive-services-bing-visual-search-signup-requirements](../../../../includes/cognitive-services-bing-visual-search-signup-requirements.md)]

## <a name="initialize-the-application"></a>Inicializar o aplicativo

1. Crie um novo arquivo Python em seu IDE ou editor favorito e adicione a seguinte instrução de `import`:

    ```python
    import requests, json
    ```

2. Crie variáveis para sua chave de assinatura, ponto de extremidade e o caminho para a imagem que você está carregando. `BASE_URI` pode ser o ponto de extremidade global abaixo ou o ponto de extremidade de [subdomínio personalizado](../../../cognitive-services/cognitive-services-custom-subdomains.md) exibido no portal do Azure para seu recurso:

    ```python

    BASE_URI = 'https://api.cognitive.microsoft.com/bing/v7.0/images/visualsearch'
    SUBSCRIPTION_KEY = 'your-subscription-key'
    imagePath = 'your-image-path'
    ```
    
    Quando você carrega uma imagem local, os dados do formulário devem incluir o cabeçalho `Content-Disposition`. Você deve definir seu parâmetro `name` como "Image" e pode definir o parâmetro `filename` como qualquer cadeia de caracteres. O conteúdo do formulário inclui os dados binários da imagem. O tamanho máximo da imagem que você pode carregar é 1 MB.
    
    ```
    --boundary_1234-abcd
    Content-Disposition: form-data; name="image"; filename="myimagefile.jpg"
    
    ÿØÿà JFIF ÖÆ68g-¤CWŸþ29ÌÄøÖ‘º«™æ±èuZiÀ)"óÓß°Î= ØJ9á+*G¦...
    
    --boundary_1234-abcd--
    ```

3. Crie um objeto Dictionary para manter as informações de cabeçalho da solicitação. Associe sua chave de assinatura à cadeia de caracteres `Ocp-Apim-Subscription-Key`, conforme mostrado abaixo:

    ```python
    HEADERS = {'Ocp-Apim-Subscription-Key': SUBSCRIPTION_KEY}
    ```

4. Crie outro dicionário para conter a imagem, que é aberta e carregada quando você envia a solicitação:

    ```python
    file = {'image' : ('myfile', open(imagePath, 'rb'))}
    ```

## <a name="parse-the-json-response"></a>Analisar a resposta JSON

1. Crie um método chamado `print_json()` para executar a resposta da API e imprima o JSON:

    ```python
    def print_json(obj):
        """Print the object as json"""
        print(json.dumps(obj, sort_keys=True, indent=2, separators=(',', ': ')))
    ```

## <a name="send-the-request"></a>Enviar a solicitação

1. Use `requests.post()` para enviar uma solicitação para o API da Pesquisa Visual do Bing. Inclua a cadeia de caracteres para o ponto de extremidade, o cabeçalho e as informações do arquivo. `response.json()` de impressão com `print_json()`:

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
> [Criar um aplicativo Web de página única Pesquisa Visual](../tutorial-bing-visual-search-single-page-app.md)
