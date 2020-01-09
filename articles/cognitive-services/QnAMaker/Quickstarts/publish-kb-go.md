---
title: 'Início rápido: publicar a base de dados de conhecimento, REST, ir QnA Maker'
titleSuffix: Azure Cognitive Services
description: Este guia de início rápido baseado em REST publica sua base de dados de conhecimento e cria um ponto de extremidade que pode ser chamado em seu aplicativo ou bot de bate-papo.
services: cognitive-services
author: diberry
manager: nitinme
ms.custom: seodec18
ms.service: cognitive-services
ms.subservice: qna-maker
ms.topic: quickstart
ms.date: 12/16/2019
ms.author: diberry
ms.openlocfilehash: c66f4f2b126b3b2b86ea55112d1bdc79b4bf4571
ms.sourcegitcommit: f4f626d6e92174086c530ed9bf3ccbe058639081
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 12/25/2019
ms.locfileid: "75447449"
---
# <a name="quickstart-publish-a-knowledge-base-in-qna-maker-using-go"></a>Início Rápido: publicar uma base de dados de conhecimento no Criador de FAQ com o Go

Este guia de introdução baseada em REST orienta-o através de publicação por meio de programação de sua base de dados de conhecimento (KB). A publicação envia por push a versão mais recente da base de dados de conhecimento para um índice de Pesquisa Cognitiva dedicado do Azure e cria um ponto de extremidade que pode ser chamado em seu aplicativo ou bot de chat.

Este início rápido chama as API do Criador de FAQ:
* [Publicar](https://docs.microsoft.com/rest/api/cognitiveservices/qnamaker/knowledgebase/publish) - esta API não requer quaisquer informações no corpo do pedido.

## <a name="prerequisites"></a>Pré-requisitos

* [Go 1.10.1](https://golang.org/dl/)
* Tem de ter um [serviço Criador de FAQ](../How-To/set-up-qnamaker-service-azure.md). Para recuperar a chave e o ponto de extremidade (que inclui o nome do recurso), selecione **início rápido** para seu recurso no portal do Azure.

* QnA Maker ID da base de dados de conhecimento (KB) encontrada na URL no parâmetro `kbid` cadeia de caracteres de consulta, conforme mostrado abaixo.

    ![ID da base de dados de conhecimento do Criador de FAQ](../media/qnamaker-quickstart-kb/qna-maker-id.png)

    Se ainda não tiver uma base de dados de conhecimento, pode criar uma de exemplo para utilizar neste guia de introdução: [Criar uma nova base de dados de conhecimento](create-new-kb-csharp.md).

> [!NOTE]
> Os arquivos de solução completos estão disponíveis no repositório do GitHub [ **Azure-Samples/cognitiva-Services-qnamaker-go** ](https://github.com/Azure-Samples/cognitive-services-qnamaker-go/tree/master/documentation-samples/quickstarts/publish-knowledge-base).

## <a name="create-a-go-file"></a>Crie um ficheiro de Go

Abra o VSCode e crie um novo ficheiro designado `publish-kb.go`.

## <a name="add-the-required-dependencies"></a>Adicionar as dependências necessárias

Na parte superior de `publish-kb.go`, adicione as linhas seguintes para adicionar as dependências necessárias ao projeto:

[!code-go[Add the required dependencies](~/samples-qnamaker-go/documentation-samples/quickstarts/publish-knowledge-base/publish-kb.go?range=3-7 "Add the required dependencies")]

## <a name="create-the-main-function"></a>Criar a função principal

Depois das dependências necessárias, adicione a seguinte classe:

```Go
package main

func main() {

}
```

## <a name="add-required-constants"></a>Adicionar constantes necessárias

Dentro do **principal**


 função, adicione as constantes necessárias para acessar o QnA Maker. Substitua os valores pelos seus próprios.

[!code-go[Add the required constants](~/samples-qnamaker-go/documentation-samples/quickstarts/publish-knowledge-base/publish-kb.go?range=16-20 "Add the required constants")]

## <a name="add-post-request-to-publish-kb"></a>Adicionar pedido POST para publicar KB

Depois das constantes necessárias, adicione o seguinte código, o que faz um pedido HTTPS para a API do QnA Maker para publicar uma base de dados de conhecimento e recebe a resposta:

[!code-go[Add a POST request to publish KB](~/samples-qnamaker-go/documentation-samples/quickstarts/get-answer/get-answer.go?range=35-48 "Add a POST request to publish KB")]

A chamada à API devolve um estado 204 para uma publicação com êxito, sem qualquer conteúdo no corpo da resposta. O código adiciona conteúdo para respostas 204.

Para qualquer outra resposta, a resposta é devolvida inalterada.

## <a name="build-and-run-the-program"></a>Compilar e executar o programa

Introduza o seguinte comando para compilar o ficheiro. A linha de comandos não devolve quaisquer informações para uma compilação bem-sucedida.

```bash
go build publish-kb.go
```

Introduza o comando seguinte numa linha de comandos para executar o programa. Irá enviar o pedido para a API do QnA Maker para publicar o KB, em seguida, imprimir 204 para êxito ou erros.

```bash
./publish-kb
```

[!INCLUDE [Clean up files and knowledge base](../../../../includes/cognitive-services-qnamaker-quickstart-cleanup-resources.md)]

## <a name="next-steps"></a>Passos seguintes

Depois da base de dados de conhecimento é publicado, é necessário o [URL de ponto final para gerar uma resposta](../Tutorials/create-publish-answer.md#generating-an-answer).

> [!div class="nextstepaction"]
> [Referência à API REST do Criador de FAQ](https://go.microsoft.com/fwlink/?linkid=2092179)
