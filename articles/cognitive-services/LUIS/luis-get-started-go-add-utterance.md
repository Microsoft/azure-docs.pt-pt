---
title: Alteração, a aplicação de comboio, Go
description: Neste guia de início rápido da linguagem Go, adicione expressões de exemplo a uma aplicação de Automatização Doméstica e prepare a aplicação.
titleSuffix: Language Understanding - Microsoft Cognitive Services
author: diberry
manager: nitinme
services: cognitive-services
ms.custom: seodec18
ms.service: cognitive-services
ms.subservice: language-understanding
ms.topic: quickstart
ms.date: 01/23/2019
ms.author: diberry
ms.openlocfilehash: 60594137306bfe58024877947959f2865fffe019
ms.sourcegitcommit: fdd6a2927976f99137bb0fcd571975ff42b2cac0
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 02/27/2019
ms.locfileid: "56961810"
---
# <a name="quickstart-change-model-using-go"></a>Início rápido: Modelo de alteração com Go

Neste início rápido, transmita expressões a um ponto final de LUIS e obtenha a intenção e as entidades.

[!INCLUDE [Quickstart introduction for endpoint](../../../includes/cognitive-services-luis-qs-endpoint-intro-para.md)]

## <a name="prerequisites"></a>Pré-requisitos

[!INCLUDE [Quickstart introduction for endpoint](../../../includes/cognitive-services-luis-qs-change-model-prereq.md)]
* Linguagem de programação do [Go](https://golang.org/) instalada.
* [VSCode](https://code.visualstudio.com) 

[!INCLUDE [Quickstart introduction for endpoint](../../../includes/cognitive-services-luis-qs-change-model-luis-repo-note.md)]

## <a name="example-utterances-json-file"></a>Ficheiro JSON de expressões de exemplo

[!INCLUDE [Quickstart introduction for endpoint](../../../includes/cognitive-services-luis-qs-change-model-json-ex-utt.md)]

## <a name="create-quickstart-code"></a>Criar código de início rápido 

1. Criar `add-utterances.go` com VSCode. 

2. Adicione dependências. 

   [!code-go[Add dependencies.](~/samples-luis/documentation-samples/quickstarts/change-model/go/add-utterances.go?range=2-10 "Add dependencies.")]

3. Adicione a função de pedido HTTP genérica, que inclui passar a chave de criação no cabeçalho. 

   [!code-go[Add HTTP request function which includes passing authoring key in header.](~/samples-luis/documentation-samples/quickstarts/change-model/go/add-utterances.go?range=12-36 "Add HTTP request function, which includes passing authoring key in header. ")]

4. Adicionar expressões de exemplo do ficheiro JSON.

   [!code-go[Add example utterances from JSON file.](~/samples-luis/documentation-samples/quickstarts/change-model/go/add-utterances.go?range=62-76 "Add example utterances from JSON file.")]

5. Peça o treino. Utiliza uma função do programa auxiliar para definir o VERBO para a mesma rota que o estado do treino. 

   [!code-go[Request training.](~/samples-luis/documentation-samples/quickstarts/change-model/go/add-utterances.go?range=77-86 "Request training. ")]

6. Estado do treino do pedido. Utiliza uma função do programa auxiliar para definir o VERBO para a mesma rota que o treino do pedido. 

   [!code-go[Request training status.](~/samples-luis/documentation-samples/quickstarts/change-model/go/add-utterances.go?range=87-90 "Request training status. ")]

7. Adicione a função principal para lidar com a análise da linha de comandos.

   [!code-go[Add main function to handle command line parsing.](~/samples-luis/documentation-samples/quickstarts/change-model/go/add-utterances.go?range=38-60 "Add main function to handle command-line parsing.")]

## <a name="add-an-utterance-from-the-command-line-train-and-get-status"></a>Adicionar uma expressão a partir da linha de comandos, treinar e obter o estado

1. Numa linha de comandos, no diretório onde criou o ficheiro do Go, introduza `go build add-utterances.go` para compilar o ficheiro do Go. A linha de comandos não devolve quaisquer informações para uma compilação bem-sucedida.

2. Execute a aplicação do Go a partir da linha de comandos ao introduzir o seguinte texto na linha de comandos: 

    ```console
    add-utterances -appID <your-app-id> -authoringKey <add-your-authoring-key> -version <your-version-id> -region westus -utteranceFile utterances.json

    ```

    Substitua `<add-your-authoring-key>` pelo valor da sua chave de criação (também conhecida como a chave de arranque). Substitua `<your-app-id>` pelo valor do seu ID da aplicação. Substitua `<your-version-id>` pelo valor da sua versão. A versão predefinida é `0.1`.

    Esta linha de comandos apresenta os resultados:

    ```console
    add example utterances requested
    [
        {
            "text": "go lang 1",
            "intentName": "None",
            "entityLabels": []
        }
    ,
        {
            "text": "go lang 2",
            "intentName": "None",
            "entityLabels": []
        }
    ]
    201
    [
        {
            "value": {
                "ExampleId": 77783998,
                "UtteranceText": "go lang 1"
            },
            "hasError": false
        },
        {
            "value": {
                "ExampleId": 77783999,
                "UtteranceText": "go lang 2"
            },
            "hasError": false
        }
    ]
    training selected
    202
    {"statusId":9,"status":"Queued"}
    training status selected
    200
    [
        {
            "modelId": "c52d6509-9261-459e-90bc-b3c872ee4a4b",
            "details": {
                "statusId": 3,
                "status": "InProgress",
                "exampleCount": 24
            }
        },
        {
            "modelId": "5119cbe8-97a1-4c1f-85e6-6449f3a38d77",
            "details": {
                "statusId": 3,
                "status": "InProgress",
                "exampleCount": 24
            }
        },
        {
            "modelId": "01e6b6bc-9872-47f9-8a52-da510cddfafe",
            "details": {
                "statusId": 3,
                "status": "InProgress",
                "exampleCount": 24
            }
        },
        {
            "modelId": "33b409b2-32b0-4b0c-9e91-31c6cfaf93fb",
            "details": {
                "statusId": 3,
                "status": "InProgress",
                "exampleCount": 24
            }
        },
        {
            "modelId": "1fb210be-2a19-496d-bb72-e0c2dd35cbc1",
            "details": {
                "statusId": 3,
                "status": "InProgress",
                "exampleCount": 24
            }
        },
        {
            "modelId": "3d098beb-a1aa-423f-a0ae-ce08ced216d6",
            "details": {
                "statusId": 3,
                "status": "InProgress",
                "exampleCount": 24
            }
        },
        {
            "modelId": "cce854f8-8f8f-4ed9-a7df-44dfea562f62",
            "details": {
                "statusId": 3,
                "status": "InProgress",
                "exampleCount": 24
            }
        },
        {
            "modelId": "4d97bf0d-5213-4502-9712-2d6e77c96045",
            "details": {
                "statusId": 3,
                "status": "InProgress",
                "exampleCount": 24
            }
        }
    ]
    ```

    Esta resposta inclui o código de estado HTTP para cada uma das três chamadas HTTP, bem como qualquer resposta JSON devolvida no corpo da resposta. 

## <a name="clean-up-resources"></a>Limpar recursos
Quando tiver terminado o início rápido, remova todos os ficheiros criados neste início rápido. 

## <a name="next-steps"></a>Passos Seguintes
> [!div class="nextstepaction"] 
> [Compilar uma aplicação Web com um domínio personalizado](luis-quickstart-intents-only.md) 
