---
title: Obter previsão com chamada REST emC#
titleSuffix: Azure Cognitive Services
services: cognitive-services
author: diberry
manager: nitinme
ms.service: cognitive-services
ms.topic: include
ms.date: 10/17/2019
ms.author: diberry
ms.openlocfilehash: 9ab477ffd7001f0f492f63355baaee26827db845
ms.sourcegitcommit: 5a8c65d7420daee9667660d560be9d77fa93e9c9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 11/15/2019
ms.locfileid: "74125583"
---
## <a name="prerequisites"></a>Pré-requisitos

* [.NET Core V 2.2 +](https://dotnet.microsoft.com/download)
* [Visual Studio Code](https://code.visualstudio.com/)
* ID da aplicação pública: df67dcdb-c37d-46af-88e1-8b97951ca1c2

## <a name="get-luis-key"></a>Obter chave LUIS

[!INCLUDE [Use authoring key for endpoint](../includes/get-key-quickstart.md)]

## <a name="get-intent-programmatically"></a>Obter a intenção através de programação

Use C# para consultar o ponto de extremidade de previsão obter [API](https://aka.ms/luis-apim-v3-prediction) para obter o resultado da previsão. 

1. Crie um novo aplicativo de console destinado C# ao idioma, com um nome de projeto e pasta de `predict-with-rest`. 

    ```console
    dotnet new console -lang C# -n predict-with-rest
    ```

1. Instale as dependências necessárias com os seguintes comandos da CLI do dotnet.

    ```console
    dotnet add package System.Net.Http
    ```
1. Substitua Program.cs pelo seguinte código:
    
   ```csharp
    using System;
    using System.Net.Http;
    using System.Web;
    
    namespace predict_with_rest
    {
        class Program
        {
            static void Main(string[] args)
            {
                // YOUR-KEY: for example, the starter key
                var key = "YOUR-KEY";
                
                // YOUR-ENDPOINT: example is westus2.api.cognitive.microsoft.com
                var endpoint = "YOUR-ENDPOINT";

                // //public sample app
                var appId = "df67dcdb-c37d-46af-88e1-8b97951ca1c2"; 
    
                var utterance = "turn on all lights";
    
                MakeRequest(key, endpoint, appId, utterance);
    
                Console.WriteLine("Hit ENTER to exit...");
                Console.ReadLine();
            }
            static async void MakeRequest(string key, string endpoint, string appId, string utterance)
            {
                var client = new HttpClient();
                var queryString = HttpUtility.ParseQueryString(string.Empty);
    
                // The request header contains your subscription key
                client.DefaultRequestHeaders.Add("Ocp-Apim-Subscription-Key", key);
    
                // The "q" parameter contains the utterance to send to LUIS
                queryString["query"] = utterance;
    
                // These optional request parameters are set to their default values
                queryString["verbose"] = "true";
                queryString["show-all-intents"] = "true";
                queryString["staging"] = "false";
                queryString["timezoneOffset"] = "0";
    
                var endpointUri = String.Format("https://{0}/luis/prediction/v3.0/apps/{1}/slots/production/predict?query={2}", endpoint, appId, queryString);
    
                var response = await client.GetAsync(endpointUri);
    
                var strResponseContent = await response.Content.ReadAsStringAsync();
                
                // Display the JSON result from LUIS
                Console.WriteLine(strResponseContent.ToString());
            }
        }
    }

   ```

1. Substitua os seguintes valores:

    * `YOUR-KEY` com sua chave inicial
    * `YOUR-ENDPOINT` com seu ponto de extremidade, por exemplo, `westus2.api.cognitive.microsoft.com`

1. Crie a aplicação da consola. 

    ```console
    dotnet build
    ```

1. Execute a aplicação de consola. A saída do console exibe o mesmo JSON que você viu anteriormente na janela do navegador.

    ```console
    dotnet run
    ```

1. Examine a resposta de previsão no formato JSON:

    ```console
    Hit ENTER to exit...
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
> [Adicionar declarações e treinar](../get-started-get-model-rest-apis.md)