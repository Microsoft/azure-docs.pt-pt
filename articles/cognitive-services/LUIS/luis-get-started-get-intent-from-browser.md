---
title: 'Início rápido: obter a intenção com o navegador-LUIS'
titleSuffix: Azure Cognitive Services
description: Neste guia de início rápido, use um aplicativo LUIS público disponível para determinar a intenção de um usuário de um texto de conversação em um navegador.
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
ms.sourcegitcommit: 4f6a7a2572723b0405a21fea0894d34f9d5b8e12
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 02/04/2020
ms.locfileid: "76987959"
---
# <a name="quickstart-get-intent-with-a-browser"></a>Início rápido: obter a intenção com um navegador

Para compreender o que devolve um ponto final de predição do LUIS, veja um resultado de predição num browser.

## <a name="prerequisites"></a>Pré-requisitos

Para consultar um aplicativo público, você precisa de:

* Sua própria Reconhecimento vocal (LUIS) a criação ou a chave de previsão que pode ser obtida no [portal do Luis (versão prévia)](https://preview.luis.ai/). Se você ainda não tiver uma assinatura para criar uma chave, poderá registrar-se para uma [conta gratuita](https://azure.microsoft.com/free/).
* A ID do aplicativo público: `df67dcdb-c37d-46af-88e1-8b97951ca1c2`.

## <a name="use-the-browser-to-see-predictions"></a>Use o navegador para ver previsões

1. Abra um browser.
1. Use as URLs completas abaixo, substituindo `YOUR-KEY` pela sua própria LUIS de criação ou chave de previsão. As solicitações são solicitações GET e incluem a autorização, com a LUIS de criação ou a chave de previsão, como um parâmetro de cadeia de caracteres de consulta.

    #### <a name="v3-prediction-requesttabv3-1-1"></a>[Solicitação de previsão v3](#tab/V3-1-1)


    O formato da URL V3 para uma solicitação **obter** ponto de extremidade (por slots) é:

    `
    https://westus.api.cognitive.microsoft.com/luis/prediction/v3.0/apps/df67dcdb-c37d-46af-88e1-8b97951ca1c2/slots/production/predict?query=turn on all lights&subscription-key=YOUR-KEY
    `

    #### <a name="v2-prediction-requesttabv2-1-2"></a>[V2 solicitação de previsão](#tab/V2-1-2)

    O formato da URL v2 para uma solicitação **obter** ponto de extremidade é:

    `
    https://westus.api.cognitive.microsoft.com/luis/v2.0/apps/df67dcdb-c37d-46af-88e1-8b97951ca1c2?subscription-key=YOUR-KEY&q=turn on all lights
    `

1. Cole o URL numa janela do browser e prima Enter. O browser apresenta um resultado JSON que indica que o LUIS deteta a intenção `HomeAutomation.TurnOn` como intenção principal e a entidade `HomeAutomation.Operation` com o valor `on`.

    #### <a name="v3-prediction-responsetabv3-2-1"></a>[Resposta de previsão v3](#tab/V3-2-1)

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

    #### <a name="v2-prediction-responsetabv2-2-2"></a>[V2 de resposta de previsão](#tab/V2-2-2)

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

1. Para ver todas as intenções, adicione o parâmetro de cadeia de caracteres de consulta apropriado.

    #### <a name="v3-prediction-endpointtabv3-3-1"></a>[Ponto de extremidade de previsão v3](#tab/V3-3-1)

    Adicione `show-all-intents=true` ao final da QueryString para **Mostrar todas as intenções**:

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

    #### <a name="v2-prediction-endpointtabv2"></a>[Ponto de extremidade de previsão v2](#tab/V2)

    Adicione `verbose=true` ao final da QueryString para **Mostrar todas as intenções**:

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

Saiba mais sobre o [ponto de extremidade de previsão v3](luis-migration-api-v3.md).

> [!div class="nextstepaction"]
> [Criar um aplicativo no portal do LUIS](get-started-portal-build-app.md)
