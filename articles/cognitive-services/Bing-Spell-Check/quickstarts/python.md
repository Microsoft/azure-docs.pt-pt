---
title: 'Quickstart: Verifique a ortografia com a REST API e Python - Bing Spell Check'
titleSuffix: Azure Cognitive Services
description: Comece a usar a API bing spell check REST para verificar a ortografia e a gramática com este arranque rápido.
services: cognitive-services
author: aahill
manager: nitinme
ms.service: cognitive-services
ms.subservice: bing-spell-check
ms.topic: quickstart
ms.date: 05/21/2020
ms.author: aahi
ms.openlocfilehash: ce0e72545c8ecd82b78d437f2c4939aff6930c3d
ms.sourcegitcommit: fc718cc1078594819e8ed640b6ee4bef39e91f7f
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 05/27/2020
ms.locfileid: "83993563"
---
# <a name="quickstart-check-spelling-with-the-bing-spell-check-rest-api-and-python"></a>Quickstart: Verifique a ortografia com o Bing Spell Check REST API e Python

Use este quickstart para fazer a sua primeira chamada para a API de Verificação de Feitiços de Bing. Este simples pedido python envia um pedido à API e devolve uma lista de correções sugeridas. 

Embora esta aplicação esteja escrita em Python, a API é um serviço Web RESTful compatível com a maioria dos idiomas de programação. O código fonte para esta aplicação está disponível no [GitHub](https://github.com/Azure-Samples/cognitive-services-REST-api-samples/blob/master/python/Search/BingEntitySearchv7.py)

## <a name="prerequisites"></a>Pré-requisitos

* Python [3.x](https://www.python.org)

[!INCLUDE [cognitive-services-bing-spell-check-signup-requirements](../../../../includes/cognitive-services-bing-spell-check-signup-requirements.md)]

## <a name="initialize-the-application"></a>Inicializar a aplicação

1. Crie um novo ficheiro Python no seu IDE ou editor favorito e adicione as seguintes declarações de importação:

   ```python
   import requests
   import json
   ```

2. Crie variáveis para o texto que pretende soletrar, a sua chave de subscrição e o seu ponto final de Verificação de Feitiços bing. Pode utilizar o ponto final global no seguinte código ou utilizar o ponto final de [subdomínio personalizado](../../../cognitive-services/cognitive-services-custom-subdomains.md) exibido no portal Azure para o seu recurso.

    ```python
    api_key = "<ENTER-KEY-HERE>"
    example_text = "Hollo, wrld" # the text to be spell-checked
    endpoint = "https://api.cognitive.microsoft.com/bing/v7.0/SpellCheck"
    ```

## <a name="create-the-parameters-for-the-request"></a>Criar os parâmetros para o pedido

1. Crie um novo dicionário com `text` como chave, e o seu texto como valor.

    ```python
    data = {'text': example_text}
    ```

2. Adicione os parâmetros para o seu pedido: 

   1. Atribua o seu código de mercado ao `mkt` parâmetro com o `=` operador. O código de mercado é o código do país/região a quem faz o pedido. 

   1. Adicione o `mode` parâmetro com o operador `&` e, em seguida, atribua o modo de verificação ortográfica. O modo pode ser `proof` (captura a maioria dos erros ortográficos/gramaticais) ou (apanha a maioria dos `spell` erros ortográficos, mas não tantos erros gramaticais). 
 
    ```python
    params = {
        'mkt':'en-us',
        'mode':'proof'
        }
    ```

3. Adicione um `Content-Type` cabeçalho e a sua chave de subscrição ao `Ocp-Apim-Subscription-Key` cabeçalho.

    ```python
    headers = {
        'Content-Type': 'application/x-www-form-urlencoded',
        'Ocp-Apim-Subscription-Key': api_key,
        }
    ```

## <a name="send-the-request-and-read-the-response"></a>Envie o pedido e leia a resposta

1. Envie o pedido do POST utilizando a biblioteca de pedidos.

    ```python
    response = requests.post(endpoint, headers=headers, params=params, data=data)
    ```

2. Obtenha a resposta json e imprima-a.

    ```python
    json_response = response.json()
    print(json.dumps(json_response, indent=4))
    ```


## <a name="run-the-application"></a>Executar a aplicação

Se estiver a utilizar a linha de comando, utilize o seguinte comando para executar a aplicação:

```bash
python <FILE_NAME>.py
```

## <a name="example-json-response"></a>Exemplo resposta JSON

É devolvida uma resposta com êxito em JSON, tal como é apresentado no exemplo seguinte:

```json
{
   "_type": "SpellCheck",
   "flaggedTokens": [
      {
         "offset": 0,
         "token": "Hollo",
         "type": "UnknownToken",
         "suggestions": [
            {
               "suggestion": "Hello",
               "score": 0.9115257530801
            },
            {
               "suggestion": "Hollow",
               "score": 0.858039839213461
            },
            {
               "suggestion": "Hallo",
               "score": 0.597385084464481
            }
         ]
      },
      {
         "offset": 7,
         "token": "wrld",
         "type": "UnknownToken",
         "suggestions": [
            {
               "suggestion": "world",
               "score": 0.9115257530801
            }
         ]
      }
   ]
}
```

## <a name="next-steps"></a>Próximos passos

> [!div class="nextstepaction"]
> [Criar uma aplicação web de página única](../tutorials/spellcheck.md)

- [O que é a API de Verificação Ortográfica do Bing?](../overview.md)
- [Referência de Bing Spell Check API v7](https://docs.microsoft.com/rest/api/cognitiveservices-bingsearch/bing-spell-check-api-v7-reference)
