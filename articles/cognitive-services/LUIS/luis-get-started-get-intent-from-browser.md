---
title: Como consultar previsões usando um browser - LUIS
description: Neste artigo, utilize uma aplicação pública disponível LUIS para determinar a intenção de um utilizador de texto de conversação num browser.
ms.service: cognitive-services
ms.subservice: language-understanding
ms.topic: quickstart
ms.date: 11/30/2020
ms.openlocfilehash: a3bad4ab69f6950f83db9cf1f49cfa4cb7c7b5f0
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/30/2021
ms.locfileid: "102040129"
---
# <a name="how-to-query-the-prediction-runtime-with-user-text"></a>Como consultar o tempo de previsão com o texto do utilizador

Para compreender o que devolve um ponto final de predição do LUIS, veja um resultado de predição num browser.

## <a name="prerequisites"></a>Pré-requisitos

Para consultar uma aplicação pública, você precisa:

* A informação de recursos da sua compreensão linguística (LUIS):
    * **Chave de previsão** - que pode ser obtida a partir do [Portal LUIS](https://www.luis.ai/). Se ainda não tiver uma subscrição para criar uma chave, pode inscrever-se numa [conta gratuita.](https://azure.microsoft.com/free/cognitive-services)
    * **Previsão subdomínio do ponto final** - o subdomínio é também o **nome** do seu recurso LUIS.
* Um ID de aplicativo LUIS - use o ID da aplicação IoT pública de `df67dcdb-c37d-46af-88e1-8b97951ca1c2` . A consulta do utilizador utilizada no código quickstart é específica para essa aplicação.

## <a name="use-the-browser-to-see-predictions"></a>Use o navegador para ver previsões

1. Abra um browser.
1. Utilize os URLs completos abaixo, `YOUR-KEY` substituindo-os pela sua própria tecla de previsão LUIS. Os pedidos são pedidos GET e incluem a autorização, com a sua chave de previsão LUIS, como parâmetro de cadeia de consulta.

    #### <a name="v3-prediction-request"></a>[Pedido de previsão V3](#tab/V3-1-1)


    O formato do URL V3 para um pedido de ponto final **GET** (por slots) é:

    `
    https://YOUR-LUIS-ENDPOINT-SUBDOMAIN.api.cognitive.microsoft.com/luis/prediction/v3.0/apps/df67dcdb-c37d-46af-88e1-8b97951ca1c2/slots/production/predict?query=turn on all lights&subscription-key=YOUR-LUIS-PREDICTION-KEY
    `

    #### <a name="v2-prediction-request"></a>[Pedido de previsão V2](#tab/V2-1-2)

    O formato do URL V2 para um pedido de ponto final **GET** é:

    `
    https://YOUR-LUIS-ENDPOINT-SUBDOMAIN.api.cognitive.microsoft.com/luis/v2.0/apps/df67dcdb-c37d-46af-88e1-8b97951ca1c2?subscription-key=YOUR-LUIS-PREDICTION-KEY&q=turn on all lights
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

1. Para ver todas as intenções, adicione o parâmetro de cadeia de consulta apropriado.

    #### <a name="v3-prediction-endpoint"></a>[Ponto final de previsão V3](#tab/V3-3-1)

    Adicione `show-all-intents=true` ao final do teste de consulta para mostrar todas as **intenções:**

    `
    https://YOUR-LUIS-ENDPOINT-SUBDOMAIN.api.cognitive.microsoft.com/luis/predict/v3.0/apps/df67dcdb-c37d-46af-88e1-8b97951ca1c2/slots/production/predict?query=turn on all lights&subscription-key=YOUR-LUIS-PREDICTION-KEY&show-all-intents=true
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

    #### <a name="v2-prediction-endpoint"></a>[Ponto final de previsão V2](#tab/V2)

    Adicione `verbose=true` ao final do teste de consulta para mostrar todas as **intenções:**

    `
    https://YOUR-LUIS-ENDPOINT-SUBDOMAIN.api.cognitive.microsoft.com/luis/v2.0/apps/df67dcdb-c37d-46af-88e1-8b97951ca1c2?q=turn on all lights&subscription-key=YOUR-LUIS-PREDICTION-KEY&verbose=true
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

## <a name="next-steps"></a>Passos seguintes

Saiba mais sobre:
* [Ponto final de previsão V3](luis-migration-api-v3.md)
* [Subdomínios personalizados](../cognitive-services-custom-subdomains.md)

> [!div class="nextstepaction"]
> [Criar uma app no portal LUIS](get-started-portal-build-app.md)
