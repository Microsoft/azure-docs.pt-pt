---
title: Obtenha intenção com chamada REST no Node.js
titleSuffix: Azure Cognitive Services
services: cognitive-services
author: diberry
manager: nitinme
ms.service: cognitive-services
ms.topic: include
ms.date: 01/31/2020
ms.author: diberry
ms.openlocfilehash: 9252fbbf0895bf821c119272ac37d3af1c91fc89
ms.sourcegitcommit: 4f6a7a2572723b0405a21fea0894d34f9d5b8e12
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 02/04/2020
ms.locfileid: "76987802"
---
## <a name="prerequisites"></a>Pré-requisitos

* Linguagem de programação [Node.js](https://nodejs.org/)
* [Visual Studio Code](https://code.visualstudio.com/)
* ID da aplicação pública: `df67dcdb-c37d-46af-88e1-8b97951ca1c2`

## <a name="create-luis-runtime-key-for-predictions"></a>Criar a chave de tempo de execução do LUIS para previsões

1. Assine no [portal Azure](https://portal.azure.com)
1. Clique em [criar **compreensão linguística** ](https://ms.portal.azure.com/#create/Microsoft.CognitiveServicesLUISAllInOne)
1. Introduza todas as definições necessárias para a tecla **Runtime:**

    |Definição|Valor|
    |--|--|
    |Nome|Nome desejado (2-64 caracteres)|
    |Subscrição|Selecione subscrição apropriada|
    |Localização|Selecione qualquer local próximo e disponível|
    |Escalão de Preço|`F0` - o nível de preços mínimos|
    |Grupo de Recursos|Selecione um grupo de recursos disponíveis|

1. Clique em **Criar** e aguarde a criação do recurso. Depois de criado, navegue para a página de recursos.
1. Colete `endpoint` configurado e um `key`.

## <a name="get-intent-programmatically"></a>Obter a intenção através de programação

Use nonó.js para consultar o [ponto final](https://aka.ms/luis-apim-v3-prediction) da previsão e obter um resultado de previsão.

1. Copie o seguinte código de corte para um ficheiro chamado `predict.js`:

    ```javascript
    var request = require('request');
    var requestpromise = require('request-promise');
    var querystring = require('querystring');

    // Analyze text
    //
    getPrediction = async () => {

        // YOUR-KEY - Language Understanding runtime key
        var endpointKey = "YOUR-KEY";

        // YOUR-ENDPOINT Language Understanding endpoint URL, an example is your-resource-name.api.cognitive.microsoft.com
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

1. Substitua os valores `YOUR-KEY` e `YOUR-ENDPOINT` pela sua própria chave de previsão **Runtime** e ponto final.

    |Proteção das|Finalidade|
    |--|--|
    |`YOUR-KEY`|A tua **chave** de previsão de 32 caracteres.|
    |`YOUR-ENDPOINT`| O seu ponto final de URL de previsão. Por exemplo, `replace-with-your-resource-name.api.cognitive.microsoft.com`.|

1. Instale as dependências `request`, `request-promise`e `querystring` com este comando:

    ```console
    npm install request request-promise querystring
    ```

1. Execute a sua aplicação com este comando:

    ```console
    node predict.js
    ```

 1. Reveja a resposta de previsão, que é devolvida como JSON:

    ```console
    {"query":"turn on all lights","prediction":{"topIntent":"HomeAutomation.TurnOn","intents":{"HomeAutomation.TurnOn":{"score":0.5375382},"None":{"score":0.08687421},"HomeAutomation.TurnOff":{"score":0.0207554}},"entities":{"HomeAutomation.Operation":["on"],"$instance":{"HomeAutomation.Operation":[{"type":"HomeAutomation.Operation","text":"on","startIndex":5,"length":2,"score":0.724984169,"modelTypeId":-1,"modelType":"Unknown","recognitionSources":["model"]}]}}}}
    ```

    A resposta json formatada para a legibilidade:

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

## <a name="clean-up-resources"></a>Limpar recursos

Ao concluir este guia de início rápido, exclua o arquivo do sistema de arquivos.

## <a name="next-steps"></a>Passos seguintes

> [!div class="nextstepaction"]
> [Adicione expressões e comboio](../get-started-get-model-rest-apis.md)