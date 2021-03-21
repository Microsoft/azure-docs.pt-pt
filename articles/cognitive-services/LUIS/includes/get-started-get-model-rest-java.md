---
title: Obtenha modelo com chamada REST em Java
titleSuffix: Azure Cognitive Services
services: cognitive-services
manager: nitinme
ms.service: cognitive-services
ms.subservice: language-understanding
ms.topic: include
ms.date: 06/03/2020
ms.custom: devx-track-java
ms.openlocfilehash: 5ece29b7189f31654056ec357f522b59ccb16587
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/19/2021
ms.locfileid: "95097672"
---
[Documentação de referência](https://westeurope.dev.cognitive.microsoft.com/docs/services/luis-programmatic-apis-v3-0-preview/operations/5890b47c39e2bb052c5b9c45)  |  [Amostra](https://github.com/Azure-Samples/cognitive-services-quickstart-code/blob/master/java/LUIS/java-model-with-rest/Model.java)

## <a name="prerequisites"></a>Pré-requisitos

* [JDK SE](/azure/developer/java/fundamentals/java-jdk-long-term-support) (Kit de Desenvolvimento do Java, Edição Standard)
* [Código visual do estúdio](https://code.visualstudio.com/) ou o seu IDE favorito

## <a name="example-utterances-json-file"></a>Ficheiro JSON de expressões de exemplo

[!INCLUDE [Quickstart explanation of example utterance JSON file](get-started-get-model-json-example-utterances.md)]

## <a name="change-model-programmatically"></a>Alterar modelo programáticamente

1. Crie uma nova pasta para realizar o seu projeto Java, como `java-model-with-rest` .

1. Faça um subdiretório nomeado `lib` e copie nas seguintes libs java no `lib` subdiretório:

    * [registo de commons-1.2.jar](https://raw.githubusercontent.com/Azure-Samples/cognitive-services-language-understanding/master/documentation-samples/quickstarts/analyze-text/java/lib/commons-logging-1.2.jar)
    * [httpclient-4.5.3.jar](https://raw.githubusercontent.com/Azure-Samples/cognitive-services-language-understanding/master/documentation-samples/quickstarts/analyze-text/java/lib/httpclient-4.5.3.jar)
    * [httpcore-4.4.6.jar](https://raw.githubusercontent.com/Azure-Samples/cognitive-services-language-understanding/master/documentation-samples/quickstarts/analyze-text/java/lib/httpcore-4.4.6.jar)

1. Crie um novo ficheiro com o nome `Model.java`. Adicione o seguinte código:

    [!code-java[Code snippet](~/cognitive-services-quickstart-code/java/LUIS/java-model-with-rest/Model.java)]

1. Substitua os valores a partir dos `YOUR-` seus próprios valores.

    |Informações|Objetivo|
    |--|--|
    |`YOUR-APP-ID`| Sua identificação de aplicativo LUIS. |
    |`YOUR-AUTHORING-KEY`|A sua chave de autoria de 32 caracteres.|
    |`YOUR-AUTHORING-ENDPOINT`| O seu ponto final de autoria de URL. Por exemplo, `https://replace-with-your-resource-name.api.cognitive.microsoft.com/`. Define o nome do seu recurso quando criou o recurso.|

    As chaves e recursos atribuídos são visíveis no portal LUIS na secção Gerir, na página de **recursos Azure.** O ID da aplicação está disponível na mesma secção Gerir, na página Definições de **Aplicação.**

1. Com um pedido de comando no mesmo diretório que criou o `Model.java` ficheiro, insira o seguinte comando para compilar o ficheiro Java:

    * Se estiver a utilizar o Windows, utilize este comando: `javac -cp ";lib/*" Model.java`
    * Se estiver a utilizar o macOS ou o Linux, utilize este comando: `javac -cp ":lib/*" Model.java`

1. Executar a aplicação Java a partir da linha de comando introduzindo o seguinte texto na solicitação de comando:

    * Se estiver a utilizar o Windows, utilize este comando: `java -cp ";lib/*" Model`
    * Se estiver a utilizar o macOS ou o Linux, utilize este comando: `java -cp ":lib/*" Model`

1. Reveja a resposta de autoria:

    ```console
    [{"value":{"ExampleId":1137150691,"UtteranceText":"order a pizza"},"hasError":false},{"value":{"ExampleId":1137150692,"UtteranceText":"order a large pepperoni pizza"},"hasError":false},{"value":{"ExampleId":1137150693,"UtteranceText":"i want two large pepperoni pizzas on thin crust"},"hasError":false}]
    {"statusId":9,"status":"Queued"}
    [{"modelId":"edb46abf-0000-41ab-beb2-a41a0fe1630f","details":{"statusId":9,"status":"Queued","exampleCount":0}},{"modelId":"a5030be2-616c-4648-bf2f-380fa9417d37","details":{"statusId":9,"status":"Queued","exampleCount":0}},{"modelId":"3f2b1f31-a3c3-4fbd-8182-e9d9dbc120b9","details":{"statusId":9,"status":"Queued","exampleCount":0}},{"modelId":"e4b6704b-1636-474c-9459-fe9ccbeba51c","details":{"statusId":9,"status":"Queued","exampleCount":0}},{"modelId":"031d3777-2a00-4a7a-9323-9a3280a30000","details":{"statusId":9,"status":"Queued","exampleCount":0}},{"modelId":"9250e7a1-06eb-4413-9432-ae132ed32583","details":{"statusId":3,"status":"InProgress","exampleCount":0,"progressSubstatus":"CollectingData"}}]
    ```

    Aqui está a saída formatada para a legibilidade:

    ```json
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
    {
      "statusId": 9,
      "status": "Queued"
    }
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
          "statusId": 3,
          "status": "InProgress",
          "exampleCount": 0,
          "progressSubstatus": "CollectingData"
        }
      }
    ]
    ```

## <a name="clean-up-resources"></a>Limpar os recursos

Quando terminar este arranque rápido, elimine a pasta do projeto do sistema de ficheiros.

## <a name="next-steps"></a>Passos seguintes

> [!div class="nextstepaction"]
> [Melhores práticas para uma aplicação](../luis-concept-best-practices.md)