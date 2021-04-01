---
title: Obtenha o modelo com chamada REST em Node.js
titleSuffix: Azure Cognitive Services
services: cognitive-services
manager: nitinme
ms.service: cognitive-services
ms.subservice: language-understanding
ms.topic: include
ms.date: 06/03/2020
ms.custom: devx-track-js
ms.openlocfilehash: fd25ef6ff44f1d62d553db518f7389da0e5d5019
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/30/2021
ms.locfileid: "91534636"
---
[Documentação de referência](https://westeurope.dev.cognitive.microsoft.com/docs/services/luis-programmatic-apis-v3-0-preview/operations/5890b47c39e2bb052c5b9c45)  |  [Amostra](https://github.com/Azure-Samples/cognitive-services-quickstart-code/blob/master/javascript/LUIS/node-model-with-rest/model.js)

## <a name="prerequisites"></a>Pré-requisitos

* Linguagem de programação [Node.js](https://nodejs.org/)
* [Visual Studio Code](https://code.visualstudio.com/)

## <a name="example-utterances-json-file"></a>Ficheiro JSON de expressões de exemplo

[!INCLUDE [Quickstart explanation of example utterance JSON file](get-started-get-model-json-example-utterances.md)]

## <a name="create-the-nodejs-project"></a>Criar o projeto Node.js

1. Crie uma nova pasta para realizar o seu projeto Node.js, como `node-model-with-rest` .

1. Abrir um novo Pedido de Comando, navegar para a pasta que criou e executar o seguinte comando:

    ```console
    npm init
    ```

    Prima Introduza em cada pedido para aceitar as definições predefinidos.

1. Instale o módulo de promessa de pedido introduzindo o seguinte comando:

    ```console
    npm install --save request
    npm install --save request-promise
    npm install --save querystring
    ```

## <a name="change-model-programmatically"></a>Alterar modelo programáticamente

1. Crie um novo ficheiro com o nome `model.js`. Adicione o seguinte código:

    [!code-javascript[Code snippet](~/cognitive-services-quickstart-code/javascript/LUIS/node-model-with-rest/model.js)]

1. Substitua os valores a partir dos `YOUR-` seus próprios valores.

    |Informações|Objetivo|
    |--|--|
    |`YOUR-APP-ID`| Sua identificação de aplicativo LUIS. |
    |`YOUR-AUTHORING-KEY`|A sua chave de autoria de 32 caracteres.|
    |`YOUR-AUTHORING-ENDPOINT`| O seu ponto final de autoria de URL. Por exemplo, `https://replace-with-your-resource-name.api.cognitive.microsoft.com/`. Define o nome do seu recurso quando criou o recurso.|

    As chaves e recursos atribuídos são visíveis no portal LUIS na secção Gerir, na página de **recursos Azure.** O ID da aplicação está disponível na mesma secção Gerir, na página Definições de **Aplicação.**

1. Na pronta do comando, insira o seguinte comando para executar o projeto:

    ```console
    node model.js
    ```

1. Reveja a resposta de autoria:

    ```json
    addUtterance:
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
    train POST:
    {
      "statusId": 9,
      "status": "Queued"
    }
    train GET:
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
    done
    ```

## <a name="clean-up-resources"></a>Limpar os recursos

Quando terminar este arranque rápido, elimine a pasta do projeto do sistema de ficheiros.

## <a name="next-steps"></a>Passos seguintes

> [!div class="nextstepaction"]
> [Melhores práticas para uma aplicação](../luis-concept-best-practices.md)