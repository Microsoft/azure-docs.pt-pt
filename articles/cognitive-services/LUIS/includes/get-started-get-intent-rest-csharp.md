---
title: 'Obtenha previsão com chamada REST em C #'
titleSuffix: Azure Cognitive Services
services: cognitive-services
author: diberry
manager: nitinme
ms.service: cognitive-services
ms.topic: include
ms.date: 04/20/2020
ms.author: diberry
ms.openlocfilehash: 20916ff80ae52ee9fc215d87c0987900d89e590a
ms.sourcegitcommit: 34a6fa5fc66b1cfdfbf8178ef5cdb151c97c721c
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/28/2020
ms.locfileid: "81733281"
---
## <a name="prerequisites"></a>Pré-requisitos

* [.NET Core V2.2+](https://dotnet.microsoft.com/download)
* [Visual Studio Code](https://code.visualstudio.com/)
* Um ID de aplicativo LUIS - use `df67dcdb-c37d-46af-88e1-8b97951ca1c2`o ID da aplicação IoT pública de . A consulta do utilizador utilizada no código de arranque rápido é específica dessa aplicação.

## <a name="create-luis-runtime-key-for-predictions"></a>Criar a chave de tempo de execução do LUIS para previsões

1. Assine no [portal Azure](https://portal.azure.com)
1. Clique em [criar **compreensão linguística** ](https://ms.portal.azure.com/#create/Microsoft.CognitiveServicesLUISAllInOne)
1. Introduza todas as definições necessárias para a tecla Runtime:

    |Definição|Valor|
    |--|--|
    |Nome|Nome desejado (2-64 caracteres)|
    |Subscrição|Selecione subscrição apropriada|
    |Localização|Selecione qualquer local próximo e disponível|
    |Escalão de Preço|`F0`- o nível de preços mínimos|
    |Grupo de Recursos|Selecione um grupo de recursos disponíveis|

1. Clique em **Criar** e aguarde a criação do recurso. Depois de criado, navegue para a página de recursos.
1. Colete configurado `endpoint` `key`e a .

## <a name="get-intent-programmatically"></a>Obter a intenção através de programação

Utilize C# (.NET Core) para consultar o [ponto final](https://aka.ms/luis-apim-v3-prediction) da previsão e obter um resultado de previsão.

1. Crie uma nova aplicação de consola direcionada para o `predict-with-rest`idioma C#, com um nome de projeto e pasta de .

    ```console
    dotnet new console -lang C# -n predict-with-rest
    ```

1. Mude para `predict-with-rest` o diretório que acabou de criar e instale dependências necessárias com estes comandos:

    ```console
    cd predict-with-rest
    dotnet add package System.Net.Http
    ```

1. Abra `Program.cs` no seu IDE favorito ou editor. Em seguida, sobrepor-se `Program.cs` com o seguinte código:

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
                // YOUR-KEY: 32 character key
                var key = "YOUR-KEY";

                // YOUR-ENDPOINT: example is your-resource-name.api.cognitive.microsoft.com
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

1. Substitua `YOUR-KEY` `YOUR-ENDPOINT` os valores e valores com a sua própria chave de previsão e ponto final.

    |Informações|Objetivo|
    |--|--|
    |`YOUR-KEY`|A tua chave de previsão de 32 caracteres.|
    |`YOUR-ENDPOINT`| O seu ponto final de URL de previsão. Por exemplo, `replace-with-your-resource-name.api.cognitive.microsoft.com`.|

1. Construa a aplicação da consola com este comando:

    ```console
    dotnet build
    ```

1. Execute a aplicação de consola. A saída da consola apresenta o mesmo JSON que viu anteriormente na janela do navegador.

    ```console
    dotnet run
    ```

1. Reveja a resposta de previsão, que é devolvida como JSON:

    ```console
    Hit ENTER to exit...
    {'query': 'turn on all lights', 'prediction': {'topIntent': 'HomeAutomation.TurnOn', 'intents': {'HomeAutomation.TurnOn': {'score': 0.5375382}, 'None': {'score': 0.08687421}, 'HomeAutomation.TurnOff': {'score': 0.0207554}}, 'entities': {'HomeAutomation.Operation': ['on'], '$instance': {'HomeAutomation.Operation': [{'type': 'HomeAutomation.Operation', 'text': 'on', 'startIndex': 5, 'length': 2, 'score': 0.724984169, 'modelTypeId': -1, 'modelType': 'Unknown', 'recognitionSources': ['model']}]}}}}
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

Quando terminar este arranque rápido, elimine o ficheiro do sistema de ficheiros.

## <a name="next-steps"></a>Passos seguintes

> [!div class="nextstepaction"]
> [Adicione expressões e comboio](../get-started-get-model-rest-apis.md)