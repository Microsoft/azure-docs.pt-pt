---
title: 'Obtenha modelo com chamada REST em C #'
titleSuffix: Azure Cognitive Services
services: cognitive-services
manager: nitinme
ms.service: cognitive-services
ms.subservice: language-understanding
ms.topic: include
ms.date: 06/03/2020
ms.openlocfilehash: 4e4a5115ee83030b5cf3bd8c047f7ccadf4c9e2c
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/19/2021
ms.locfileid: "91534623"
---
[Documentação de referência](https://westeurope.dev.cognitive.microsoft.com/docs/services/luis-programmatic-apis-v3-0-preview/operations/5890b47c39e2bb052c5b9c45)  |  [Amostra](https://github.com/Azure-Samples/cognitive-services-quickstart-code/blob/master/python/LUIS/python-model-with-rest/model.py)

## <a name="prerequisites"></a>Pré-requisitos

* [Python 3.6](https://www.python.org/downloads/) ou mais tarde.
* [Visual Studio Code](https://code.visualstudio.com/)

## <a name="example-utterances-json-file"></a>Ficheiro JSON de expressões de exemplo

[!INCLUDE [Quickstart explanation of example utterance JSON file](get-started-get-model-json-example-utterances.md)]

## <a name="create-pizza-app"></a>Criar aplicativo Pizza

[!INCLUDE [Create pizza app](get-started-get-model-create-pizza-app.md)]

## <a name="change-model-programmatically"></a>Alterar modelo programáticamente

1. Crie um novo ficheiro com o nome `model.py`. Adicione o seguinte código:

    [!code-python[Add example utterances to Language Understanding in python](~/cognitive-services-quickstart-code/python/LUIS/python-model-with-rest/model.py)]

1. Substitua os valores a partir dos `YOUR-` seus próprios valores.

    |Informações|Objetivo|
    |--|--|
    |`YOUR-APP-ID`| Sua identificação de aplicativo LUIS. |
    |`YOUR-AUTHORING-KEY`|A sua chave de autoria de 32 caracteres.|
    |`YOUR-AUTHORING-ENDPOINT`| O seu ponto final de autoria de URL. Por exemplo, `https://replace-with-your-resource-name.api.cognitive.microsoft.com/`. Define o nome do seu recurso quando criou o recurso.|

    As chaves e recursos atribuídos são visíveis no portal LUIS na secção Gerir, na página de **recursos Azure.** O ID da aplicação está disponível na mesma secção Gerir, na página Definições de **Aplicação.**

1. Com um pedido de comando no mesmo diretório que criou o ficheiro, insira o seguinte comando para executar o ficheiro:

    ```console
    python model.py
    ```

1. Reveja a resposta de autoria:

    ```console
    Add the list of utterances:
    [{'value': {'ExampleId': 1137150691, 'UtteranceText': 'order a pizza'}, 'hasError': False}, {'value': {'ExampleId': 1137150692, 'UtteranceText': 'order a large pepperoni pizza'}, 'hasError': False}, {'value': {'ExampleId': 1137150693, 'UtteranceText': 'i want two large pepperoni pizzas on thin crust'}, 'hasError': False}]
    Request training:
    {'statusId': 9, 'status': 'Queued'}
    Request training status:
    [{'modelId': 'edb46abf-0000-41ab-beb2-a41a0fe1630f', 'details': {'statusId': 3, 'status': 'InProgress', 'exampleCount': 0, 'progressSubstatus': 'CollectingData'}}, {'modelId': 'a5030be2-616c-4648-bf2f-380fa9417d37', 'details': {'statusId': 3, 'status': 'InProgress', 'exampleCount': 0, 'progressSubstatus': 'CollectingData'}}, {'modelId': '3f2b1f31-a3c3-4fbd-8182-e9d9dbc120b9', 'details': {'statusId': 3, 'status': 'InProgress', 'exampleCount': 0, 'progressSubstatus': 'CollectingData'}}, {'modelId': 'e4b6704b-1636-474c-9459-fe9ccbeba51c', 'details': {'statusId': 3, 'status': 'InProgress', 'exampleCount': 0, 'progressSubstatus': 'CollectingData'}}, {'modelId': '031d3777-2a00-4a7a-9323-9a3280a30000', 'details': {'statusId': 3, 'status': 'InProgress', 'exampleCount': 0, 'progressSubstatus': 'CollectingData'}}, {'modelId': '9250e7a1-06eb-4413-9432-ae132ed32583', 'details': {'statusId': 3, 'status': 'InProgress', 'exampleCount': 0, 'progressSubstatus': 'CollectingData'}}]
    ```

    Aqui está a saída formatada para a legibilidade:

    ```json
    Add the list of utterances:
    [
      {
        'value': {
          'ExampleId': 1137150691,
          'UtteranceText': 'order a pizza'
        },
        'hasError': False
      },
      {
        'value': {
          'ExampleId': 1137150692,
          'UtteranceText': 'order a large pepperoni pizza'
        },
        'hasError': False
      },
      {
        'value': {
          'ExampleId': 1137150693,
          'UtteranceText': 'i want two large pepperoni pizzas on thin crust'
        },
        'hasError': False
      }
    ]

    Request training:
    {
      'statusId': 9,
      'status': 'Queued'
    }

    Request training status:
    [
      {
        'modelId': 'edb46abf-0000-41ab-beb2-a41a0fe1630f',
        'details': {
          'statusId': 3,
          'status': 'InProgress',
          'exampleCount': 0,
          'progressSubstatus': 'CollectingData'
        }
      },
      {
        'modelId': 'a5030be2-616c-4648-bf2f-380fa9417d37',
        'details': {
          'statusId': 3,
          'status': 'InProgress',
          'exampleCount': 0,
          'progressSubstatus': 'CollectingData'
        }
      },
      {
        'modelId': '3f2b1f31-a3c3-4fbd-8182-e9d9dbc120b9',
        'details': {
          'statusId': 3,
          'status': 'InProgress',
          'exampleCount': 0,
          'progressSubstatus': 'CollectingData'
        }
      },
      {
        'modelId': 'e4b6704b-1636-474c-9459-fe9ccbeba51c',
        'details': {
          'statusId': 3,
          'status': 'InProgress',
          'exampleCount': 0,
          'progressSubstatus': 'CollectingData'
        }
      },
      {
        'modelId': '031d3777-2a00-4a7a-9323-9a3280a30000',
        'details': {
          'statusId': 3,
          'status': 'InProgress',
          'exampleCount': 0,
          'progressSubstatus': 'CollectingData'
        }
      },
      {
        'modelId': '9250e7a1-06eb-4413-9432-ae132ed32583',
        'details': {
          'statusId': 3,
          'status': 'InProgress',
          'exampleCount': 0,
          'progressSubstatus': 'CollectingData'
        }
      }
    ]
    ```

## <a name="clean-up-resources"></a>Limpar os recursos

Quando terminar com este arranque rápido, elimine o ficheiro do sistema de ficheiros.

## <a name="next-steps"></a>Passos seguintes

> [!div class="nextstepaction"]
> [Melhores práticas para uma aplicação](../luis-concept-best-practices.md)
