---
title: Obtenha intenção com chamada REST in Go
titleSuffix: Azure Cognitive Services
services: cognitive-services
author: diberry
manager: nitinme
ms.service: cognitive-services
ms.topic: include
ms.date: 01/31/2020
ms.author: diberry
ms.openlocfilehash: 02cb7738e20df6aba8690c9fe2ee718144bad114
ms.sourcegitcommit: 4f6a7a2572723b0405a21fea0894d34f9d5b8e12
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 02/04/2020
ms.locfileid: "76987804"
---
## <a name="prerequisites"></a>Pré-requisitos

* Linguagem de programação [Go](https://golang.org/)
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

Use Ir para consultar o [ponto final](https://aka.ms/luis-apim-v3-prediction) da previsão e obter um resultado de previsão.

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

        // YOUR-KEY - your **Runtime** key
        var endpointKey = "YOUR-KEY"

        // YOUR-ENDPOINT - example is your-resource-name.api.cognitive.microsoft.com
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

1. Substitua os valores `YOUR-KEY` e `YOUR-ENDPOINT` pela sua própria chave de previsão **Runtime** e ponto final.

    |Proteção das|Finalidade|
    |--|--|
    |`YOUR-KEY`|A tua **chave** de previsão de 32 caracteres.|
    |`YOUR-ENDPOINT`| O seu ponto final de URL de previsão. Por exemplo, `replace-with-your-resource-name.api.cognitive.microsoft.com`.|

1. Com um pedido de comando no mesmo diretório onde criou o ficheiro, insira o seguinte comando para compilar o ficheiro Go:

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

    JSON formatado para a legibilidade:

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


## <a name="clean-up-resources"></a>Limpar recursos

Ao concluir este guia de início rápido, exclua o arquivo do sistema de arquivos.

## <a name="next-steps"></a>Passos seguintes

> [!div class="nextstepaction"]
> [Adicione expressões e comboio](../get-started-get-model-rest-apis.md)