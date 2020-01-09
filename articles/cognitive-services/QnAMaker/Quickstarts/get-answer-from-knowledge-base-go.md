---
title: 'Início rápido: obter resposta da base de dados de conhecimento-REST, ir QnA Maker'
titleSuffix: Azure Cognitive Services
description: Este guia de início rápido baseado em REST orienta você pela obtenção de uma resposta de uma base de dados de conhecimento, programaticamente.
services: cognitive-services
author: diberry
manager: nitinme
ms.service: cognitive-services
ms.subservice: qna-maker
ms.topic: quickstart
ms.date: 12/16/2019
ms.author: diberry
ms.openlocfilehash: fdab3b970b64a3f4e2999c7cdb92d04bc0c36ac0
ms.sourcegitcommit: f4f626d6e92174086c530ed9bf3ccbe058639081
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 12/25/2019
ms.locfileid: "75447528"
---
# <a name="quickstart-get-answers-to-a-question-from-a-knowledge-base-with-go"></a>Início rápido: Obtenha respostas para uma pergunta de uma base de dados de conhecimento com go

Este guia de início rápido orienta você na obtenção de uma resposta de uma base de dados de conhecimento QnA Maker publicada. A base de conhecimento contém perguntas e respostas de [fontes de dados](../Concepts/data-sources-supported.md) , como perguntas frequentes. A [pergunta](../how-to/metadata-generateanswer-usage.md#generateanswer-request-configuration) é enviada para o serviço de QnA Maker. A [resposta](../how-to/metadata-generateanswer-usage.md#generateanswer-response-properties) inclui a resposta mais prevista.

[Documentação de referência](https://docs.microsoft.com/rest/api/cognitiveservices/qnamakerruntime/runtime) | [exemplo](https://github.com/Azure-Samples/cognitive-services-qnamaker-go/blob/master/documentation-samples/quickstarts/get-answer/get-answer.go)

## <a name="prerequisites"></a>Pré-requisitos

* [Go 1.10.1](https://golang.org/dl/)
* [Visual Studio Code](https://code.visualstudio.com/)
* Tem de ter um [serviço Criador de FAQ](../How-To/set-up-qnamaker-service-azure.md). Para obter a sua chave, selecione **chaves** sob **gestão de recursos** no dashboard do Azure para o seu recurso do QnA Maker.
* **Publicar** configurações da página. Se você não tiver uma base de dados de conhecimento publicada, crie uma base de dados de conhecimento vazia, importe uma base de dados de conhecimento na página **configurações** e, em seguida, publique. Você pode baixar e usar [essa base de dados de conhecimento básica](https://github.com/Azure-Samples/cognitive-services-sample-data-files/blob/master/qna-maker/knowledge-bases/basic-kb.tsv).

    As configurações da página de publicação incluem o valor da rota de POST, o valor do host e o valor de EndpointKey.

    ![Definições de publicação](../media/qnamaker-quickstart-get-answer/publish-settings.png)

## <a name="create-a-go-file"></a>Crie um ficheiro de Go

Abra VSCode e crie um novo arquivo chamado `get-answer.go` e adicione a seguinte classe:

```Go
package main

func main() {

}
```

## <a name="add-the-required-dependencies"></a>Adicionar as dependências necessárias

Acima da função `main`, na parte superior do arquivo de `get-answer.go`, adicione as dependências necessárias ao projeto:

[!code-go[Add the required dependencies](~/samples-qnamaker-go/documentation-samples/quickstarts/get-answer/get-answer.go?range=3-9 "Add the required dependencies")]

## <a name="add-the-required-constants"></a>Adicionar as constantes necessárias

Na parte superior da função `main`, adicione as constantes necessárias para acessar QnA Maker. Esses valores estão na página **publicar** depois que você publicar a base de dados de conhecimento.

[!code-go[Add the required constants](~/samples-qnamaker-go/documentation-samples/quickstarts/get-answer/get-answer.go?range=17-33 "Add the required constants")]

## <a name="add-a-post-request-to-send-question-and-get-answer"></a>Adicionar uma solicitação POST para enviar pergunta e obter resposta

O código a seguir faz uma solicitação HTTPS para o API de QnA Maker para enviar a pergunta para a base de dados de conhecimento e recebe a resposta:

[!code-go[Add a POST request to send question to knowledge base](~/samples-qnamaker-go/documentation-samples/quickstarts/get-answer/get-answer.go?range=35-48 "Add a POST request to send question to knowledge base")]

O valor do cabeçalho de `Authorization` inclui a cadeia de caracteres `EndpointKey`.

Saiba mais sobre a [solicitação](../how-to/metadata-generateanswer-usage.md#generateanswer-request) e a [resposta](../how-to/metadata-generateanswer-usage.md#generateanswer-response).

## <a name="build-and-run-the-program"></a>Compilar e executar o programa

Crie e execute o programa a partir da linha de comando. Ele enviará automaticamente o pedido para a API QnA Maker, em seguida, será impresso para a janela da consola.

1. Crie o ficheiro:

    ```bash
    go build get-answer.go
    ```

1. Execute o ficheiro:

    ```bash
    ./get-answer
    ```

[!INCLUDE [JSON request and response](../../../../includes/cognitive-services-qnamaker-quickstart-get-answer-json.md)]


[!INCLUDE [Clean up files and knowledge base](../../../../includes/cognitive-services-qnamaker-quickstart-cleanup-resources.md)]

## <a name="next-steps"></a>Passos seguintes

> [!div class="nextstepaction"]
> [Referência à API REST do Criador de FAQ](https://go.microsoft.com/fwlink/?linkid=2092179)
