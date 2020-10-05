---
title: 'Quickstart: Use cURL e REST para obter intenção - LUIS'
titleSuffix: Azure Cognitive Services
description: Neste rest API quickstart, use cURL e REST para determinar a intenção do utilizador de texto de conversação.
services: cognitive-services
author: nitinme
manager: diberry
ms.custom: seodec18
ms.service: cognitive-services
ms.subservice: language-understanding
ms.topic: quickstart
ms.date: 06/19/2020
ms.author: nitinme
ms.openlocfilehash: 7a8cbfa307f14a6660dcace3315384bc523ad888
ms.sourcegitcommit: eb6bef1274b9e6390c7a77ff69bf6a3b94e827fc
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/05/2020
ms.locfileid: "91316261"
---
# <a name="quickstart-use-curl-and-rest-to-get-intent"></a>Quickstart: Use cURL e REST para obter intenção

Este quickstart irá mostrar-lhe como usar uma aplicação LUIS para determinar a intenção de um utilizador a partir de texto de conversação. Utilizará o cURL para enviar a intenção do utilizador como texto para o ponto final de previsão HTTP da aplicação Pizza. No ponto final, a LUIS aplica o modelo da app Pizza para analisar o texto de linguagem natural para significado, determinando a intenção geral e extraindo dados relevantes para o domínio da app.

[Documentação de referência](https://westeurope.dev.cognitive.microsoft.com/docs/services/luis-programmatic-apis-v3-0-preview/operations/5890b47c39e2bb052c5b9c08)

## <a name="prerequisites"></a>Pré-requisitos

* Uma conta [LIVRE LUIS.](https://www.luis.ai)
* Um editor de texto como [Visual Studio Code.](https://code.visualstudio.com/)
* O programa de linha de comando cURL. O programa cURL já está instalado no macOS, na maioria das distribuições do Linux, e no Windows 10 constrói 1803 e mais tarde. Se precisar de instalar o cURL, pode descarregar o cURL a partir da página de [descarregamento cURL](https://curl.haxx.se/download.html).

## <a name="create-pizza-app"></a>Criar aplicativo Pizza

[!INCLUDE [Create pizza app](includes/get-started-get-model-create-pizza-app.md)]

## <a name="get-intent-from-the-prediction-endpoint"></a>Obtenha intenção do ponto final da previsão

Use o cURL para consultar o [ponto final](https://aka.ms/luis-apim-v3-prediction) da previsão e obter um resultado de previsão.

1. Copie este comando para o seu editor de texto:

    #### <a name="windows"></a>[Windows](#tab/windows)
    ```cURL
    curl "https://***YOUR-PREDICTION-ENDPOINT***/luis/prediction/v3.0/apps/***YOUR-APP-ID***/slots/production/predict" ^
          --request GET ^
          --get ^
          --data "subscription-key=***YOUR-PREDICTION-KEY***" ^
          --data "verbose=true" ^
          --data "show-all-intents=true" ^
          --data-urlencode "query=I want two large pepperoni pizzas on thin crust please"
    ```

    #### <a name="macos"></a>[macOS](#tab/unix)

    ```cURL
    curl "https://***YOUR-PREDICTION-ENDPOINT***/luis/prediction/v3.0/apps/***YOUR-APP-ID***/slots/production/predict" \
          --request GET \
          --get \
          --data "subscription-key=***YOUR-PREDICTION-KEY***" \
          --data "verbose=true" \
          --data "show-all-intents=true" \
          --data-urlencode "query=I want two large pepperoni pizzas on thin crust please"
    ```

    #### <a name="linux"></a>[Linux](#tab/linux)

    ```cURL
    curl "https://***YOUR-PREDICTION-ENDPOINT***/luis/prediction/v3.0/apps/***YOUR-APP-ID***/slots/production/predict" \
          --request GET \
          --get \
          --data "subscription-key=***YOUR-PREDICTION-KEY***" \
          --data "verbose=true" \
          --data "show-all-intents=true" \
          --data-urlencode "query=I want two large pepperoni pizzas on thin crust please"
    ```

    ***

1. Substitua os valores a partir dos `***YOUR-` seus próprios valores.

    |Informações|Objetivo|
    |--|--|
    |`***YOUR-PREDICTION-ENDPOINT***`| O seu ponto final de PREVISÃO URL. Localizada no portal LUIS, página Azure Resources para a sua aplicação.<br>Por exemplo, `https://westus.api.cognitive.microsoft.com/`.|
    |`***YOUR-APP-ID***`|A sua identificação de aplicativo. Localizado no portal LUIS, página de Definições de Aplicação para a sua aplicação.
    |`***YOUR-PREDICTION-KEY***`|A tua chave de previsão de caracteres de 32 caracteres. Localizada no portal LUIS, página Azure Resources para a sua aplicação.

1. Copie o texto para uma janela da consola e prima Enter para executar o comando:

1. Execute o seu script com este comando de consola:

    ```console
    python predict.py
    ```

1. Reveja a resposta de previsão, que é devolvida como JSON:

    ```console
    {"query":"I want two large pepperoni pizzas on thin crust please","prediction":{"topIntent":"ModifyOrder","intents":{"ModifyOrder":{"score":1.0},"None":{"score":8.55E-09},"Greetings":{"score":1.82222226E-09},"CancelOrder":{"score":1.47272727E-09},"Confirmation":{"score":9.8125E-10}},"entities":{"Order":[{"FullPizzaWithModifiers":[{"PizzaType":["pepperoni pizzas"],"Size":[["Large"]],"Quantity":[2],"Crust":[["Thin"]],"$instance":{"PizzaType":[{"type":"PizzaType","text":"pepperoni pizzas","startIndex":17,"length":16,"score":0.9978157,"modelTypeId":1,"modelType":"Entity Extractor","recognitionSources":["model"]}],"Size":[{"type":"SizeList","text":"large","startIndex":11,"length":5,"score":0.9984481,"modelTypeId":1,"modelType":"Entity Extractor","recognitionSources":["model"]}],"Quantity":[{"type":"builtin.number","text":"two","startIndex":7,"length":3,"score":0.999770939,"modelTypeId":1,"modelType":"Entity Extractor","recognitionSources":["model"]}],"Crust":[{"type":"CrustList","text":"thin crust","startIndex":37,"length":10,"score":0.933985531,"modelTypeId":1,"modelType":"Entity Extractor","recognitionSources":["model"]}]}}],"$instance":{"FullPizzaWithModifiers":[{"type":"FullPizzaWithModifiers","text":"two large pepperoni pizzas on thin crust","startIndex":7,"length":40,"score":0.90681237,"modelTypeId":1,"modelType":"Entity Extractor","recognitionSources":["model"]}]}}],"ToppingList":[["Pepperoni"]],"$instance":{"Order":[{"type":"Order","text":"two large pepperoni pizzas on thin crust","startIndex":7,"length":40,"score":0.9047088,"modelTypeId":1,"modelType":"Entity Extractor","recognitionSources":["model"]}],"ToppingList":[{"type":"ToppingList","text":"pepperoni","startIndex":17,"length":9,"modelTypeId":5,"modelType":"List Entity Extractor","recognitionSources":["model"]}]}}}}
    ```

    Resposta JSON formatada para a legibilidade:

    ```JSON
    {
      "query": "I want two large pepperoni pizzas on thin crust please",
      "prediction": {
        "topIntent": "ModifyOrder",
        "intents": {
          "ModifyOrder": {
            "score": 1.0
          },
          "None": {
            "score": 8.55e-9
          },
          "Greetings": {
            "score": 1.82222226e-9
          },
          "CancelOrder": {
            "score": 1.47272727e-9
          },
          "Confirmation": {
            "score": 9.8125e-10
          }
        },
        "entities": {
          "Order": [
            {
              "FullPizzaWithModifiers": [
                {
                  "PizzaType": [
                    "pepperoni pizzas"
                  ],
                  "Size": [
                    [
                      "Large"
                    ]
                  ],
                  "Quantity": [
                    2
                  ],
                  "Crust": [
                    [
                      "Thin"
                    ]
                  ],
                  "$instance": {
                    "PizzaType": [
                      {
                        "type": "PizzaType",
                        "text": "pepperoni pizzas",
                        "startIndex": 17,
                        "length": 16,
                        "score": 0.9978157,
                        "modelTypeId": 1,
                        "modelType": "Entity Extractor",
                        "recognitionSources": [
                          "model"
                        ]
                      }
                    ],
                    "Size": [
                      {
                        "type": "SizeList",
                        "text": "large",
                        "startIndex": 11,
                        "length": 5,
                        "score": 0.9984481,
                        "modelTypeId": 1,
                        "modelType": "Entity Extractor",
                        "recognitionSources": [
                          "model"
                        ]
                      }
                    ],
                    "Quantity": [
                      {
                        "type": "builtin.number",
                        "text": "two",
                        "startIndex": 7,
                        "length": 3,
                        "score": 0.999770939,
                        "modelTypeId": 1,
                        "modelType": "Entity Extractor",
                        "recognitionSources": [
                          "model"
                        ]
                      }
                    ],
                    "Crust": [
                      {
                        "type": "CrustList",
                        "text": "thin crust",
                        "startIndex": 37,
                        "length": 10,
                        "score": 0.933985531,
                        "modelTypeId": 1,
                        "modelType": "Entity Extractor",
                        "recognitionSources": [
                          "model"
                        ]
                      }
                    ]
                  }
                }
              ],
              "$instance": {
                "FullPizzaWithModifiers": [
                  {
                    "type": "FullPizzaWithModifiers",
                    "text": "two large pepperoni pizzas on thin crust",
                    "startIndex": 7,
                    "length": 40,
                    "score": 0.90681237,
                    "modelTypeId": 1,
                    "modelType": "Entity Extractor",
                    "recognitionSources": [
                      "model"
                    ]
                  }
                ]
              }
            }
          ],
          "ToppingList": [
            [
              "Pepperoni"
            ]
          ],
          "$instance": {
            "Order": [
              {
                "type": "Order",
                "text": "two large pepperoni pizzas on thin crust",
                "startIndex": 7,
                "length": 40,
                "score": 0.9047088,
                "modelTypeId": 1,
                "modelType": "Entity Extractor",
                "recognitionSources": [
                  "model"
                ]
              }
            ],
            "ToppingList": [
              {
                "type": "ToppingList",
                "text": "pepperoni",
                "startIndex": 17,
                "length": 9,
                "modelTypeId": 5,
                "modelType": "List Entity Extractor",
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

## <a name="next-steps"></a>Passos seguintes

> [!div class="nextstepaction"]
> [Adicione expressões e comboio](luis-get-started-rest-get-model.md)
