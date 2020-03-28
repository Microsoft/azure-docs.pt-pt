---
title: 'Quickstart: Obtenha intenção com o navegador - LUIS'
titleSuffix: Azure Cognitive Services
description: Neste arranque rápido, utilize uma aplicação LIVRE pública disponível para determinar a intenção de um utilizador a partir de texto de conversação num browser.
services: cognitive-services
author: diberry
manager: nitinme
ms.custom: seodec18
ms.service: cognitive-services
ms.subservice: language-understanding
ms.topic: quickstart
ms.date: 02/03/2020
ms.author: diberry
ms.openlocfilehash: e06bb4c09b3ebab25c0c0ef8ac5c51f6842f34cd
ms.sourcegitcommit: 9ee0cbaf3a67f9c7442b79f5ae2e97a4dfc8227b
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/27/2020
ms.locfileid: "76987959"
---
# <a name="quickstart-get-intent-with-a-browser"></a>Quickstart: Obtenha intenção com um navegador

Para compreender o que devolve um ponto final de predição do LUIS, veja um resultado de predição num browser.

## <a name="prerequisites"></a>Pré-requisitos

Para consultar uma aplicação pública, precisa de:

* A sua própria chave de compreensão linguística (LUIS) de autoria ou previsão que pode ser obtida a partir do [Portal LUIS (Pré-visualização)](https://preview.luis.ai/). Se ainda não tiver uma subscrição para criar uma chave, pode inscrever-se para uma [conta gratuita.](https://azure.microsoft.com/free/)
* Id da aplicação `df67dcdb-c37d-46af-88e1-8b97951ca1c2`pública: .

## <a name="use-the-browser-to-see-predictions"></a>Use o navegador para ver previsões

1. Abra um browser.
1. Utilize os URLs completos `YOUR-KEY` abaixo, substituindo pela sua própria chave DE AUTOR E Previsão LUIS. Os pedidos são pedidos GET e incluem a autorização, com a sua chave DE Autor ou Previsão LUIS, como parâmetro de corda de consulta.

    #### <a name="v3-prediction-request"></a>[Pedido de previsão V3](#tab/V3-1-1)


    O formato do URL V3 para **um** pedido de ponto final GET (por ranhuras) é:

    `
    https://westus.api.cognitive.microsoft.com/luis/prediction/v3.0/apps/df67dcdb-c37d-46af-88e1-8b97951ca1c2/slots/production/predict?query=turn on all lights&subscription-key=YOUR-KEY
    `

    #### <a name="v2-prediction-request"></a>[Pedido de previsão V2](#tab/V2-1-2)

    O formato do URL V2 para **um** pedido de ponto final get é:

    `
    https://westus.api.cognitive.microsoft.com/luis/v2.0/apps/df67dcdb-c37d-46af-88e1-8b97951ca1c2?subscription-key=YOUR-KEY&q=turn on all lights
    `

1. Cole o URL numa janela do browser e prima Enter. O browser apresenta um resultado JSON que indica que o LUIS deteta a intenção `HomeAutomation.TurnOn` como intenção principal e a entidade `HomeAutomation.Operation` com o valor `on`.

    #### <a name="v3-prediction-response"></a>[Resposta de previsão V3](#tab/V3-2-1)

    ```JSON
    {
        "query": "turn on all lights",
        "prediction": {
            "topIntent": "HomeAutomation.TurnOn",
            "intents": {
                "HomeAutomation.TurnOn": {
                    "score": 0.5375382
                }
            },
            "entities": {
                "HomeAutomation.Operation": [
                    "on"
                ]
            }
        }
    }
    ```

    #### <a name="v2-prediction-response"></a>[Resposta de previsão V2](#tab/V2-2-2)

    ```json
    {
        "query": "turn on all lights",
        "topScoringIntent": {
            "intent": "HomeAutomation.TurnOn",
            "score": 0.5375382
        },
        "entities": [
            {
                "entity": "on",
                "type": "HomeAutomation.Operation",
                "startIndex": 5,
                "endIndex": 6,
                "score": 0.724984169
            }
        ]
    }
    ```

    * * *

1. Para ver todas as intenções, adicione o parâmetro de corda de consulta apropriado.

    #### <a name="v3-prediction-endpoint"></a>[Ponto final de previsão V3](#tab/V3-3-1)

    Adicione `show-all-intents=true` ao fim do fio de consulta para **mostrar todas as intenções:**

    `
    https://westus.api.cognitive.microsoft.com/luis/predict/v3.0/apps/df67dcdb-c37d-46af-88e1-8b97951ca1c2/slots/production/predict?query=turn on all lights&subscription-key=YOUR-KEY&show-all-intents=true
    `

    ```JSON
    {
        "query": "turn off the living room light",
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
                ]
            }
        }
    }
    ```

    #### <a name="v2-prediction-endpoint"></a>[Ponto final da previsão V2](#tab/V2)

    Adicione `verbose=true` ao fim do fio de consulta para **mostrar todas as intenções:**

    `
    https://westus.api.cognitive.microsoft.com/luis/v2.0/apps/df67dcdb-c37d-46af-88e1-8b97951ca1c2?q=turn on all lights&subscription-key={your-key}&verbose=true
    `

    ```json
    {
        "query": "turn on all lights",
        "topScoringIntent": {
            "intent": "HomeAutomation.TurnOn",
            "score": 0.5375382
        },
        "intents": [
            {
                "intent": "HomeAutomation.TurnOn",
                "score": 0.5375382
            },
            {
                "intent": "None",
                "score": 0.08687421
            },
            {
                "intent": "HomeAutomation.TurnOff",
                "score": 0.0207554
            }
        ],
        "entities": [
            {
                "entity": "on",
                "type": "HomeAutomation.Operation",
                "startIndex": 5,
                "endIndex": 6,
                "score": 0.724984169
            }
        ]
    }
    ```


<!-- FIX - is the public app getting updated for the new prebuilt domain with entities? -->

## <a name="next-steps"></a>Passos seguintes

Saiba mais sobre o ponto final da [previsão V3](luis-migration-api-v3.md).

> [!div class="nextstepaction"]
> [Criar uma app no portal LUIS](get-started-portal-build-app.md)
