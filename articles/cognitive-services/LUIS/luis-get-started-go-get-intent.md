---
title: Get intenção, Go
titleSuffix: Language Understanding - Azure Cognitive Services
description: Neste guia de introdução do Go, utilize uma aplicação do LUIS pública disponível para determinar a intenção de um utilizador de texto de conversação.
services: cognitive-services
author: diberry
manager: cgronlun
ms.custom: seodec18
ms.service: cognitive-services
ms.component: language-understanding
ms.topic: quickstart
ms.date: 01/23/2019
ms.author: diberry
ms.openlocfilehash: e77bd2abcb5810810debc7782b717db47620baaa
ms.sourcegitcommit: 8115c7fa126ce9bf3e16415f275680f4486192c1
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 01/24/2019
ms.locfileid: "54847463"
---
# <a name="quickstart-get-intent-using-go"></a>Início rápido: Objetivo de GET com Go

Neste início rápido, transmita expressões a um ponto final de LUIS e obtenha a intenção e as entidades.

[!INCLUDE [Quickstart introduction for endpoint](../../../includes/cognitive-services-luis-qs-endpoint-intro-para.md)]

## <a name="prerequisites"></a>Pré-requisitos

* Linguagem de programação [Go](https://golang.org/)  
* [Visual Studio Code](https://code.visualstudio.com/)
* ID da aplicação pública: df67dcdb-c37d-46af-88e1-8b97951ca1c2

[!INCLUDE [Use authoring key for endpoint](../../../includes/cognitive-services-luis-qs-endpoint-luis-repo-note.md)]

## <a name="get-luis-key"></a>Obter chave LUIS

[!INCLUDE [Use authoring key for endpoint](../../../includes/cognitive-services-luis-qs-endpoint-get-key-para.md)]

## <a name="get-intent-with-browser"></a>Obter a intenção com o browser

[!INCLUDE [Use authoring key for endpoint](../../../includes/cognitive-services-luis-qs-endpoint-browser-para.md)]

## <a name="get-intent-programmatically"></a>Obter a intenção através de programação

Pode utilizar o Go para aceder aos mesmos resultados que viu na janela do browser no passo anterior. 

1. Crie um novo ficheiro com o nome `endpoint.go`. Adicione o seguinte código:
    
   [!code-go[Go code that calls a LUIS endpoint](~/samples-luis/documentation-samples/quickstarts/analyze-text/go/endpoint.go?range=36-98)]

2. Numa linha de comandos, no mesmo diretório onde criou o ficheiro, introduza `go build endpoint.go` para compilar o ficheiro Go. A linha de comandos não devolve informações para uma compilação bem-sucedida.

3. Execute a aplicação Go a partir da linha de comandos ao introduzir o seguinte texto na linha de comandos: 

    ```CMD
    go run endpoint.go -appID df67dcdb-c37d-46af-88e1-8b97951ca1c2 -endpointKey <add-your-key> -region westus
    ```
    
    Substitua `<add-your-key>` pelo valor da sua chave.  
    
    A resposta da linha de comandos é: 
    
    ```CMD
    appID has value df67dcdb-c37d-46af-88e1-8b97951ca1c2
    endpointKey has value xxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxx
    region has value westus
    utterance has value turn on the bedroom light
    response
    {
        "query": "turn on the bedroom light",
        "topScoringIntent": {
            "intent": "HomeAutomation.TurnOn",
            "score": 0.809439957
        },
        "entities": [
            {
            "entity": "bedroom",
            "type": "HomeAutomation.Room",
            "startIndex": 12,
            "endIndex": 18,
            "score": 0.8065475
            }
        ]
    }
    ```
    
## <a name="luis-keys"></a>Chaves LUIS

[!INCLUDE [Use authoring key for endpoint](../../../includes/cognitive-services-luis-qs-endpoint-key-usage-para.md)]

## <a name="clean-up-resources"></a>Limpar recursos
Feche o ficheiro Go e remova-o do sistema de ficheiros. 

## <a name="next-steps"></a>Passos Seguintes
> [!div class="nextstepaction"]
> [Adicionar expressões](luis-get-started-go-add-utterance.md)