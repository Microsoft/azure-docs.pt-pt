---
title: Obter a intenção com a chamada REST no Python
titleSuffix: Azure Cognitive Services
services: cognitive-services
author: diberry
manager: nitinme
ms.service: cognitive-services
ms.topic: include
ms.date: 10/18/2019
ms.author: diberry
ms.openlocfilehash: 491d97411cec65d4f747495a6246b4c62d33e973
ms.sourcegitcommit: c22327552d62f88aeaa321189f9b9a631525027c
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 11/04/2019
ms.locfileid: "73499626"
---
## <a name="prerequisites"></a>Pré-requisitos

* [Python 3.6](https://www.python.org/downloads/) ou posterior.
* [Visual Studio Code](https://code.visualstudio.com/)

## <a name="get-luis-key"></a>Obter chave LUIS

[!INCLUDE [Use authoring key for endpoint](../includes/get-key-quickstart.md)]

## <a name="get-intent--programmatically"></a>Obter a intenção através de programação

Use o Python para consultar o ponto de extremidade de previsão obter [API](https://aka.ms/luis-apim-v3-prediction) para obter o resultado da previsão.

1. Copie um dos seguintes fragmentos de código para um ficheiro chamado `predict.py`:

    ```python
    ########### Python 3.6 #############
    import requests
    
    try:
    
        key = 'YOUR-KEY'
        endpoint = 'YOUR-ENDPOINT' # such as 'westus2.api.cognitive.microsoft.com' 
        appId = 'df67dcdb-c37d-46af-88e1-8b97951ca1c2'
        utterance = 'turn on all lights'
    
        headers = {
        }
    
        params ={
            'query': utterance,
            'timezoneOffset': '0',
            'verbose': 'true',
            'show-all-intents': 'true',
            'spellCheck': 'false',
            'staging': 'false',
            'subscription-key': key
        }
    
        r = requests.get(f'https://{endpoint}/luis/prediction/v3.0/apps/{appId}/slots/production/predict',headers=headers, params=params)
        print(r.json())
    
    except Exception as e:
        print(f'{e}')
    ```

1. Substitua os seguintes valores:

    * `YOUR-KEY` com sua chave inicial
    * `YOUR-ENDPOINT` com seu ponto de extremidade, por exemplo, `westus2.api.cognitive.microsoft.com`

1. Instale as dependências com o seguinte comando de console:

    ```console
    pip install requests
    ```

1. Execute o script com o seguinte comando de console:

    ```console
    python predict.py
    ``` 

1. Examine a resposta de previsão no formato JSON:

    ```console
    {'query': 'turn on all lights', 'prediction': {'topIntent': 'HomeAutomation.TurnOn', 'intents': {'HomeAutomation.TurnOn': {'score': 0.5375382}, 'None': {'score': 0.08687421}, 'HomeAutomation.TurnOff': {'score': 0.0207554}}, 'entities': {'HomeAutomation.Operation': ['on'], '$instance': {'HomeAutomation.Operation': [{'type': 'HomeAutomation.Operation', 'text': 'on', 'startIndex': 5, 'length': 2, 'score': 0.724984169, 'modelTypeId': -1, 'modelType': 'Unknown', 'recognitionSources': ['model']}]}}}}
    ```

    A resposta JSON formatada para facilitar a leitura: 

    ```JSON
    {
        "query": "turn on all lights",
        "prediction": {
            "topIntent": "HomeAutomation.TurnOn",
            "intents": {
                "HomeAutomation.TurnOn": {
                    "score": 0.5375382
                },
                "None": {
                    "score": 0.08687421
                },
                "HomeAutomation.TurnOff": {
                    "score": 0.0207554
                }
            },
            "entities": {
                "HomeAutomation.Operation": [
                    "on"
                ],
                "$instance": {
                    "HomeAutomation.Operation": [
                        {
                            "type": "HomeAutomation.Operation",
                            "text": "on",
                            "startIndex": 5,
                            "length": 2,
                            "score": 0.724984169,
                            "modelTypeId": -1,
                            "modelType": "Unknown",
                            "recognitionSources": [
                                "model"
                            ]
                        }
                    ]
                }
            }
        }
    }
    ```

## <a name="luis-keys"></a>Chaves LUIS

[!INCLUDE [Use authoring key for endpoint](../includes/starter-key-explanation.md)]

## <a name="clean-up-resources"></a>Limpar recursos

Ao concluir este guia de início rápido, exclua o arquivo do sistema de arquivos. 

## <a name="next-steps"></a>Passos seguintes

> [!div class="nextstepaction"]
> [Adicionar declarações e treinar](../luis-get-started-python-add-utterance.md)