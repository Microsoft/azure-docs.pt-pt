---
title: 'Início rápido: Obter resposta da base de dados de conhecimento - REST, de qualquer lugar - QnA Maker'
titlesuffix: Azure Cognitive Services
description: Este guia de introdução baseada em REST ir explica como obter uma resposta de uma base de dados de conhecimento, por meio de programação.
services: cognitive-services
author: diberry
manager: nitinme
ms.service: cognitive-services
ms.subservice: qna-maker
ms.topic: quickstart
ms.date: 02/28/2019
ms.author: diberry
ms.openlocfilehash: be134267bbb1ca372006bc1ff05d7a5ddc3732de
ms.sourcegitcommit: 3f4ffc7477cff56a078c9640043836768f212a06
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/04/2019
ms.locfileid: "57311108"
---
# <a name="get-answers-to-a-question-from-a-knowledge-base-with-go"></a>Obtenha respostas a uma pergunta de uma base de dados de conhecimento com Go

Neste início rápido explica como obter programaticamente uma resposta de uma base de dados de conhecimento publicada do QnA Maker. A base de dados de conhecimento contém perguntas e respostas partir [origens de dados](../Concepts/data-sources-supported.md) como FAQs. O [pergunta](../how-to/metadata-generateanswer-usage.md#generateanswer-request-configuration) é enviado para o serviço QnA Maker. O [resposta](../how-to/metadata-generateanswer-usage.md#generateanswer-response-properties) inclui a resposta previstos para parte superior. 

## <a name="prerequisites"></a>Pré-requisitos

* [Go 1.10.1](https://golang.org/dl/)
* [Visual Studio Code](https://code.visualstudio.com/)
* Tem de ter um [serviço Criador de FAQ](../How-To/set-up-qnamaker-service-azure.md). Para obter a sua chave, selecione **chaves** sob **gestão de recursos** no dashboard do Azure para o seu recurso do QnA Maker. 
* **Publicar** página Definições. Se não tiver uma base de dados de conhecimento publicada, criar uma base de dados de conhecimento vazia, em seguida, importar uma base de dados de conhecimento sobre o **definições** página, em seguida, publique. Pode transferir e utilizar [esta base de dados de conhecimento básico](https://github.com/Azure-Samples/cognitive-services-sample-data-files/blob/master/qna-maker/knowledge-bases/basic-kb.tsv). 

    As definições de página de publicação incluem o valor de rotas de publicação, o valor do anfitrião e o valor de EndpointKey. 

    ![Definições de publicação](../media/qnamaker-quickstart-get-answer/publish-settings.png)

O código para este início rápido está no [ https://github.com/Azure-Samples/cognitive-services-qnamaker-Go ](https://github.com/Azure-Samples/cognitive-services-qnamaker-Go/tree/master/documentation-samples/quickstarts/get-answer) repositório. 

## <a name="create-a-go-file"></a>Crie um ficheiro de Go

Abra o VSCode e crie um novo ficheiro designado `get-answer.go` e adicione a seguinte classe:

```Go
package main

func main() {

}
```

## <a name="add-the-required-dependencies"></a>Adicionar as dependências necessárias

Acima do `main` função, na parte superior a `get-answer.go` de ficheiros, adicione as dependências necessárias ao projeto:

[!code-go[Add the required dependencies](~/samples-qnamaker-go/documentation-samples/quickstarts/get-answer/get-answer.go?range=3-9 "Add the required dependencies")]

## <a name="add-the-required-constants"></a>Adicionar as constantes necessárias

Na parte superior do `main` de função, adicione as constantes necessárias para acessar o QnA Maker. Estes valores são sobre o **publicar** página depois de publicar a base de dados de conhecimento. 

[!code-go[Add the required constants](~/samples-qnamaker-go/documentation-samples/quickstarts/get-answer/get-answer.go?range=17-33 "Add the required constants")]

## <a name="add-a-post-request-to-send-question-and-get-answer"></a>Adicionar um pedido POST para enviar a pergunta e obter resposta

O código a seguir faz um pedido HTTPS para a API do QnA Maker para enviar a pergunta para a base de dados de conhecimento e recebe a resposta:

[!code-go[Add a POST request to send question to knowledge base](~/samples-qnamaker-go/documentation-samples/quickstarts/get-answer/get-answer.go?range=35-48 "Add a POST request to send question to knowledge base")]

O `Authorization` valor do cabeçalho inclui a cadeia de caracteres `EndpointKey `. 

Saiba mais sobre o [solicitação](../how-to/metadata-generateanswer-usage.md#generateanswer-request) e [resposta](../how-to/metadata-generateanswer-usage.md#generateanswer-response).

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

## <a name="next-steps"></a>Passos Seguintes

> [!div class="nextstepaction"]
> [Referência à API REST do Criador de FAQ](https://westus.dev.cognitive.microsoft.com/docs/services/5a93fcf85b4ccd136866eb37/operations/5ac266295b4ccd1554da75ff)
