---
title: Obter a intenção com a chamada REST em go
titleSuffix: Azure Cognitive Services
services: cognitive-services
author: diberry
manager: nitinme
ms.service: cognitive-services
ms.topic: include
ms.date: 10/17/2019
ms.author: diberry
ms.openlocfilehash: 6d2955a77c06f371975a7a14675eedceab7c98ae
ms.sourcegitcommit: 5a8c65d7420daee9667660d560be9d77fa93e9c9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 11/15/2019
ms.locfileid: "74125580"
---
## <a name="prerequisites"></a>Pré-requisitos

* Linguagem de programação [Go](https://golang.org/)  
* [Visual Studio Code](https://code.visualstudio.com/)
* ID da aplicação pública: df67dcdb-c37d-46af-88e1-8b97951ca1c2

## <a name="get-luis-key"></a>Obter chave LUIS

[!INCLUDE [Use authoring key for endpoint](../includes/get-key-quickstart.md)]

## <a name="get-intent-programmatically"></a>Obter a intenção através de programação

Use Go para consultar o ponto de extremidade de previsão obter [API](https://aka.ms/luis-apim-v3-prediction) para obter o resultado da previsão.

1. Crie um novo ficheiro com o nome `predict.go`. Adicione o seguinte código:
    
    ```go
    package main
    
    /* Do dependencies */
    import (
        "fmt"
        "net/http"
        "net/url"
        "io/ioutil"
        "log"
    )
    func main() {
        
        // public app
        var appID = "df67dcdb-c37d-46af-88e1-8b97951ca1c2"
        
        // utterance for public app
        var utterance = "turn on all lights"
        
        // YOUR-KEY - your starter or prediction key
        var endpointKey = "YOUR-KEY"
        
        // YOUR-ENDPOINT - example is westus2.api.cognitive.microsoft.com
        var endpoint = "YOUR-ENDPOINT"
    
        endpointPrediction(appID, endpointKey, endpoint, utterance)
    }
    func endpointPrediction(appID string, endpointKey string, endpoint string, utterance string) {
    
        var endpointUrl = fmt.Sprintf("https://%s/luis/prediction/v3.0/apps/%s/slots/production/predict?subscription-key=%s&verbose=true&show-all-intents=true&query=%s", endpoint, appID, endpointKey, url.QueryEscape(utterance))
        
        response, err := http.Get(endpointUrl)
    
        if err!=nil {
            // handle error
            fmt.Println("error from Get")
            log.Fatal(err)
        }
        
        response2, err2 := ioutil.ReadAll(response.Body)
    
        if err2!=nil {
            // handle error
            fmt.Println("error from ReadAll")
            log.Fatal(err2)
        }
    
        fmt.Println("response")
        fmt.Println(string(response2))
    }
    ```

1. Substitua os seguintes valores:

    * `YOUR-KEY` com sua chave inicial
    * `YOUR-ENDPOINT` com seu ponto de extremidade, por exemplo, `westus2.api.cognitive.microsoft.com`

1. Com um prompt de comando no mesmo diretório em que você criou o arquivo, digite o seguinte comando para compilar o arquivo Go:

    ```console
    go build predict.go
    ```  

1. Execute a aplicação do Go a partir da linha de comandos ao introduzir o seguinte texto na linha de comandos: 

    ```console
    go run predict.go
    ```
    
    A resposta da linha de comandos é: 
    
    ```console
    appID has value df67dcdb-c37d-46af-88e1-8b97951ca1c2
    endpointKey has value a7b206911f714e71a1ddae36928a61cc
    endpoint has value westus2.api.cognitive.microsoft.com
    utterance has value turn on all lights
    response
    {"query":"turn on all lights","prediction":{"topIntent":"HomeAutomation.TurnOn","intents":{"HomeAutomation.TurnOn":{"score":0.5375382},"None":{"score":0.08687421},"HomeAutomation.TurnOff":{"score":0.0207554}},"entities":{"HomeAutomation.Operation":["on"],"$instance":{"HomeAutomation.Operation":[{"type":"HomeAutomation.Operation","text":"on","startIndex":5,"length":2,"score":0.724984169,"modelTypeId":-1,"modelType":"Unknown","recognitionSources":["model"]}]}}}}
    ```

    JSON formatado para facilitar a leitura:

    ```json
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