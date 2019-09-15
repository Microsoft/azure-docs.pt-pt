---
title: 'Início rápido: Verifique a ortografia com a API REST do Verificação Ortográfica do Bing e o Python'
titleSuffix: Azure Cognitive Services
description: Comece a usar a API REST do Verificação Ortográfica do Bing para verificar a ortografia e a gramática.
services: cognitive-services
author: aahill
manager: nitinme
ms.service: cognitive-services
ms.subservice: bing-spell-check
ms.topic: quickstart
ms.date: 09/13/2019
ms.author: aahi
ms.openlocfilehash: fef329a46594071b5f1621e9a46140f407eeb415
ms.sourcegitcommit: 1752581945226a748b3c7141bffeb1c0616ad720
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 09/14/2019
ms.locfileid: "70996725"
---
# <a name="quickstart-check-spelling-with-the-bing-spell-check-rest-api-and-python"></a>Início rápido: Verifique a ortografia com a API REST do Verificação Ortográfica do Bing e o Python

Use este guia de início rápido para fazer sua primeira chamada para a API REST do Verificação Ortográfica do Bing. Esse aplicativo simples do Python envia uma solicitação para a API e retorna uma lista de correções sugeridas. Embora esta aplicação esteja escrita em Python, a API é um serviço Web RESTful compatível com a maioria das linguagens de programação. O código-fonte para este aplicativo está disponível no [GitHub](https://github.com/Azure-Samples/cognitive-services-REST-api-samples/blob/master/python/Search/BingEntitySearchv7.py)

## <a name="prerequisites"></a>Pré-requisitos

* Python [3. x](https://www.python.org)

[!INCLUDE [cognitive-services-bing-spell-check-signup-requirements](../../../../includes/cognitive-services-bing-spell-check-signup-requirements.md)]

## <a name="initialize-the-application"></a>Inicializar o aplicativo

1. Crie um novo arquivo Python em seu IDE ou editor favorito e adicione a seguinte instrução de importação.

   ```python
   import requests
   import json
   ```

2. Crie variáveis para o texto no qual você deseja verificar a ortografia, sua chave de assinatura e seu ponto de extremidade de Verificação Ortográfica do Bing.

    ```python
    api_key = "<ENTER-KEY-HERE>"
    example_text = "Hollo, wrld" # the text to be spell-checked
    endpoint = "https://api.cognitive.microsoft.com/bing/v7.0/SpellCheck"
    ```

## <a name="create-the-parameters-for-the-request"></a>Criar os parâmetros para a solicitação

1. Crie um novo dicionário com `text` como a chave e o texto como o valor.

    ```python
    data = {'text': example_text}
    ```

2. Adicione os parâmetros para sua solicitação. Acrescente seu código de mercado `mkt=`após. O código do mercado é o país do qual você faz a solicitação. Além disso, acrescente seu modo de verificação ortográfica `&mode=`após. O `proof` modo é (captura a maioria dos erros de ortografia/gramática `spell` ) ou (captura a maior parte da ortografia, mas não a quantidade de erros gramaticais).

    ```python
    params = {
        'mkt':'en-us',
        'mode':'proof'
        }
    ```

3. Adicione um `Content-Type` cabeçalho e sua chave de assinatura `Ocp-Apim-Subscription-Key` ao cabeçalho.

    ```python
    headers = {
        'Content-Type': 'application/x-www-form-urlencoded',
        'Ocp-Apim-Subscription-Key': api_key,
        }
    ```

## <a name="send-the-request-and-read-the-response"></a>Enviar a solicitação e ler a resposta

1. Envie a solicitação POST usando a biblioteca de solicitações.

    ```python
    response = requests.post(endpoint, headers=headers, params=params, data=data)
    ```

2. Obtenha a resposta JSON e imprima-a.

    ```python
    json_response = response.json()
    print(json.dumps(json_response, indent=4))
    ```

## <a name="example-json-response"></a>Exemplo de resposta JSON

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

## <a name="next-steps"></a>Passos seguintes

> [!div class="nextstepaction"]
> [Criar um aplicativo Web de página única](../tutorials/spellcheck.md)

- [O que é a API de Verificação Ortográfica do Bing?](../overview.md)
- [Bing Spell Check API v7 Reference](https://docs.microsoft.com/rest/api/cognitiveservices-bingsearch/bing-spell-check-api-v7-reference) (Referência da API de Verificação de Ortografia do Bing v7)
