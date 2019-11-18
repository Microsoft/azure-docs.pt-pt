---
title: Obter a intenção com a chamada REST no node. js
titleSuffix: Azure Cognitive Services
services: cognitive-services
author: diberry
manager: nitinme
ms.service: cognitive-services
ms.topic: include
ms.date: 10/18/2019
ms.author: diberry
ms.openlocfilehash: 10216b52ded61328a73e5401549a59e6abc4667a
ms.sourcegitcommit: 5a8c65d7420daee9667660d560be9d77fa93e9c9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 11/15/2019
ms.locfileid: "74125581"
---
## <a name="prerequisites"></a>Pré-requisitos

* Linguagem de programação [Node.js](https://nodejs.org/) 
* [Visual Studio Code](https://code.visualstudio.com/)
* ID da aplicação pública: df67dcdb-c37d-46af-88e1-8b97951ca1c2

## <a name="get-luis-key"></a>Obter chave LUIS

[!INCLUDE [Use authoring key for endpoint](../includes/get-key-quickstart.md)]

## <a name="get-intent-programmatically"></a>Obter a intenção através de programação

Use o Node. js para consultar o ponto de extremidade de previsão obter [API](https://aka.ms/luis-apim-v3-prediction) para obter o resultado da previsão.

1. Copie o trecho de código a seguir para um arquivo chamado `predict.js`:

    ```javascript
    var request = require('request');
    var requestpromise = require('request-promise');
    var querystring = require('querystring');
    
    // Analyze text
    //
    getPrediction = async () => {
    
        // YOUR-KEY - Language Understanding starter key
        var endpointKey = "YOUR-KEY";
    
        // YOUR-ENDPOINT Language Understanding endpoint URL, an example is westus2.api.cognitive.microsoft.com
        var endpoint = "YOUR-ENDPOINT";
    
        // Set the LUIS_APP_ID environment variable 
        // to df67dcdb-c37d-46af-88e1-8b97951ca1c2, which is the ID
        // of a public sample application.    
        var appId = "df67dcdb-c37d-46af-88e1-8b97951ca1c2";
    
        var utterance = "turn on all lights";
    
        // Create query string 
        var queryParams = {
            "show-all-intents": true,
            "verbose":  true,
            "query": utterance,
            "subscription-key": endpointKey
        }
    
        // append query string to endpoint URL
        var URI = `https://${endpoint}/luis/prediction/v3.0/apps/${appId}/slots/production/predict?${querystring.stringify(queryParams)}`
    
        // HTTP Request
        const response = await requestpromise(URI);
    
        // HTTP Response
        console.log(response);
    
    }
    
    // Pass an utterance to the sample LUIS app
    getPrediction().then(()=>console.log("done")).catch((err)=>console.log(err));
    ```

1. Defina os seguintes valores:

    * `YOUR-KEY` à sua chave de início
    * `YOUR-ENDPOINT` à URL do ponto de extremidade

1. Instale as dependências executando o seguinte comando na linha de comando: 

    ```console
    npm install request request-promise querystring
    ```

1. Execute o código com o seguinte comando:

    ```console
    node predict.js
    ```

 1. Examine a resposta de previsão no formato JSON:   
    
    ```console
    {"query":"turn on all lights","prediction":{"topIntent":"HomeAutomation.TurnOn","intents":{"HomeAutomation.TurnOn":{"score":0.5375382},"None":{"score":0.08687421},"HomeAutomation.TurnOff":{"score":0.0207554}},"entities":{"HomeAutomation.Operation":["on"],"$instance":{"HomeAutomation.Operation":[{"type":"HomeAutomation.Operation","text":"on","startIndex":5,"length":2,"score":0.724984169,"modelTypeId":-1,"modelType":"Unknown","recognitionSources":["model"]}]}}}}
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
> [Adicionar declarações e treinar](../get-started-get-model-rest-apis.md)