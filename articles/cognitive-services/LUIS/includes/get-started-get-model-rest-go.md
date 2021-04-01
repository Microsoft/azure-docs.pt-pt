---
title: Obtenha modelo com chamada REST em Go
titleSuffix: Azure Cognitive Services
services: cognitive-services
manager: nitinme
ms.service: cognitive-services
ms.subservice: language-understanding
ms.topic: include
ms.date: 06/03/2020
ms.openlocfilehash: 0db01cbd281b2060c1e08e6c44202624faefc5d7
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/30/2021
ms.locfileid: "91534658"
---
[Documentação de referência](https://westeurope.dev.cognitive.microsoft.com/docs/services/luis-programmatic-apis-v3-0-preview/operations/5890b47c39e2bb052c5b9c45)  |  [Amostra](https://github.com/Azure-Samples/cognitive-services-quickstart-code/blob/master/go/LUIS/go-rest-model/model.go)

## <a name="prerequisites"></a>Pré-requisitos

* Linguagem de programação [Go](https://golang.org/)
* [Visual Studio Code](https://code.visualstudio.com/)

## <a name="example-utterances-json-file"></a>Ficheiro JSON de expressões de exemplo

[!INCLUDE [Quickstart explanation of example utterance JSON file](get-started-get-model-json-example-utterances.md)]

## <a name="create-pizza-app"></a>Criar aplicativo Pizza

[!INCLUDE [Create pizza app](get-started-get-model-create-pizza-app.md)]

## <a name="change-model-programmatically"></a>Alterar modelo programáticamente

1. Crie um novo ficheiro com o nome `predict.go`. Adicione o seguinte código:

    [!code-go[Code snippet](~/cognitive-services-quickstart-code/go/LUIS/go-rest-model/model.go)]

1. Substitua os valores a partir dos `YOUR-` seus próprios valores.

    |Informações|Objetivo|
    |--|--|
    |`YOUR-APP-ID`| Sua identificação de aplicativo LUIS. |
    |`YOUR-AUTHORING-KEY`|A sua chave de autoria de 32 caracteres.|
    |`YOUR-AUTHORING-ENDPOINT`| O seu ponto final de autoria de URL. Por exemplo, `https://replace-with-your-resource-name.api.cognitive.microsoft.com/`. Define o nome do seu recurso quando criou o recurso.|

    As chaves e recursos atribuídos são visíveis no portal LUIS na secção Gerir, na página de **recursos Azure.** O ID da aplicação está disponível na mesma secção Gerir, na página Definições de **Aplicação.**

1. Com um pedido de comando no mesmo diretório que criou o ficheiro, insira o seguinte comando para compilar o ficheiro Go:

    ```console
    go build model.go
    ```

1. Execute a aplicação Go a partir da linha de comandos ao introduzir o seguinte texto na linha de comandos:

    ```console
    go run model.go
    ```

1. Reveja a resposta de autoria:

    ```console
    add example utterances requested
    body
    [{'text': 'order a pizza', 'intentName': 'ModifyOrder', 'entityLabels': [{'entityName': 'Order', 'startCharIndex': 6, 'endCharIndex': 12}]}, {'text': 'order a large pepperoni pizza', 'intentName': 'ModifyOrder', 'entityLabels': [{'entityName': 'Order', 'startCharIndex': 6, 'endCharIndex': 28}, {'entityName': 'FullPizzaWithModifiers', 'startCharIndex': 6, 'endCharIndex': 28}, {'entityName': 'PizzaType', 'startCharIndex': 14, 'endCharIndex': 28}, {'entityName': 'Size', 'startCharIndex': 8, 'endCharIndex': 12}]}, {'text': 'I want two large pepperoni pizzas on thin crust', 'intentName': 'ModifyOrder', 'entityLabels': [{'entityName': 'Order', 'startCharIndex': 7, 'endCharIndex': 46}, {'entityName': 'FullPizzaWithModifiers', 'startCharIndex': 7, 'endCharIndex': 46}, {'entityName': 'PizzaType', 'startCharIndex': 17, 'endCharIndex': 32}, {'entityName': 'Size', 'startCharIndex': 11, 'endCharIndex': 15}, {'entityName': 'Quantity', 'startCharIndex': 7, 'endCharIndex': 9}, {'entityName': 'Crust', 'startCharIndex': 37, 'endCharIndex': 46}]}]
        201
    [{"value":{"ExampleId":1137150691,"UtteranceText":"order a pizza"},"hasError":false},{"value":{"ExampleId":1137150692,"UtteranceText":"order a large pepperoni pizza"},"hasError":false},{"value":{"ExampleId":1137150693,"UtteranceText":"i want two large pepperoni pizzas on thin crust"},"hasError":false}]
    training selected
    body

        202
    {"statusId":9,"status":"Queued"}
    training status selected
    body

        200
    [{"modelId":"edb46abf-0000-41ab-beb2-a41a0fe1630f","details":{"statusId":9,"status":"Queued","exampleCount":0}},{"modelId":"a5030be2-616c-4648-bf2f-380fa9417d37","details":{"statusId":9,"status":"Queued","exampleCount":0}},{"modelId":"3f2b1f31-a3c3-4fbd-8182-e9d9dbc120b9","details":{"statusId":9,"status":"Queued","exampleCount":0}},{"modelId":"e4b6704b-1636-474c-9459-fe9ccbeba51c","details":{"statusId":9,"status":"Queued","exampleCount":0}},{"modelId":"031d3777-2a00-4a7a-9323-9a3280a30000","details":{"statusId":9,"status":"Queued","exampleCount":0}},{"modelId":"9250e7a1-06eb-4413-9432-ae132ed32583","details":{"statusId":9,"status":"Queued","exampleCount":0}}]
    ```

    Aqui está a saída formatada para a legibilidade:

    ```json
    add example utterances requested
    body
    [
      {
        'text': 'order a pizza',
        'intentName': 'ModifyOrder',
        'entityLabels': [
          {
            'entityName': 'Order',
            'startCharIndex': 6,
            'endCharIndex': 12
          }
        ]
      },
      {
        'text': 'order a large pepperoni pizza',
        'intentName': 'ModifyOrder',
        'entityLabels': [
          {
            'entityName': 'Order',
            'startCharIndex': 6,
            'endCharIndex': 28
          },
          {
            'entityName': 'FullPizzaWithModifiers',
            'startCharIndex': 6,
            'endCharIndex': 28
          },
          {
            'entityName': 'PizzaType',
            'startCharIndex': 14,
            'endCharIndex': 28
          },
          {
            'entityName': 'Size',
            'startCharIndex': 8,
            'endCharIndex': 12
          }
        ]
      },
      {
        'text': 'I want two large pepperoni pizzas on thin crust',
        'intentName': 'ModifyOrder',
        'entityLabels': [
          {
            'entityName': 'Order',
            'startCharIndex': 7,
            'endCharIndex': 46
          },
          {
            'entityName': 'FullPizzaWithModifiers',
            'startCharIndex': 7,
            'endCharIndex': 46
          },
          {
            'entityName': 'PizzaType',
            'startCharIndex': 17,
            'endCharIndex': 32
          },
          {
            'entityName': 'Size',
            'startCharIndex': 11,
            'endCharIndex': 15
          },
          {
            'entityName': 'Quantity',
            'startCharIndex': 7,
            'endCharIndex': 9
          },
          {
            'entityName': 'Crust',
            'startCharIndex': 37,
            'endCharIndex': 46
          }
        ]
      }
    ]

        201
    [
      {
        "value": {
          "ExampleId": 1137150691,
          "UtteranceText": "order a pizza"
        },
        "hasError": false
      },
      {
        "value": {
          "ExampleId": 1137150692,
          "UtteranceText": "order a large pepperoni pizza"
        },
        "hasError": false
      },
      {
        "value": {
          "ExampleId": 1137150693,
          "UtteranceText": "i want two large pepperoni pizzas on thin crust"
        },
        "hasError": false
      }
    ]
    training selected
    body

        202
    {
      "statusId": 9,
      "status": "Queued"
    }
    training status selected
    body

        200
    [
      {
        "modelId": "edb46abf-0000-41ab-beb2-a41a0fe1630f",
        "details": {
          "statusId": 9,
          "status": "Queued",
          "exampleCount": 0
        }
      },
      {
        "modelId": "a5030be2-616c-4648-bf2f-380fa9417d37",
        "details": {
          "statusId": 9,
          "status": "Queued",
          "exampleCount": 0
        }
      },
      {
        "modelId": "3f2b1f31-a3c3-4fbd-8182-e9d9dbc120b9",
        "details": {
          "statusId": 9,
          "status": "Queued",
          "exampleCount": 0
        }
      },
      {
        "modelId": "e4b6704b-1636-474c-9459-fe9ccbeba51c",
        "details": {
          "statusId": 9,
          "status": "Queued",
          "exampleCount": 0
        }
      },
      {
        "modelId": "031d3777-2a00-4a7a-9323-9a3280a30000",
        "details": {
          "statusId": 9,
          "status": "Queued",
          "exampleCount": 0
        }
      },
      {
        "modelId": "9250e7a1-06eb-4413-9432-ae132ed32583",
        "details": {
          "statusId": 9,
          "status": "Queued",
          "exampleCount": 0
        }
      }
    ]
    ```

## <a name="clean-up-resources"></a>Limpar os recursos

Quando terminar com este arranque rápido, elimine o ficheiro do sistema de ficheiros.

## <a name="next-steps"></a>Passos seguintes

> [!div class="nextstepaction"]
> [Melhores práticas para uma aplicação](../luis-concept-best-practices.md)