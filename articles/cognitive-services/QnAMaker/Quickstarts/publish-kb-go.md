---
title: 'Quickstart: Publicar base de conhecimento, REST, Go - QnA Maker'
description: Este quickstart baseado em Go REST publica a sua base de conhecimento e cria um ponto final que pode ser chamado na sua aplicação ou chat bot.
ms.service: cognitive-services
ms.subservice: qna-maker
ms.date: 02/08/2020
ROBOTS: NOINDEX,NOFOLLOW
ms.custom: RESTCURL2020FEB27
ms.topic: how-to
ms.openlocfilehash: d876be45789041cddba269784a9c1e62e5907bc2
ms.sourcegitcommit: 9eda79ea41c60d58a4ceab63d424d6866b38b82d
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 11/30/2020
ms.locfileid: "96352257"
---
# <a name="quickstart-publish-a-knowledge-base-in-qna-maker-using-go"></a>Início Rápido: publicar uma base de dados de conhecimento no Criador de FAQ com o Go

Este quickstart baseado em REST acompanha-o através da publicação programática da sua base de conhecimento (KB). A publicação empurra a versão mais recente da base de conhecimento para um índice dedicado de Pesquisa Cognitiva Azure e cria um ponto final que pode ser chamado na sua aplicação ou chat bot.

Este início rápido chama as API do Criador de FAQ:
* [Publicar](/rest/api/cognitiveservices/qnamaker/knowledgebase/publish) - esta API não requer quaisquer informações no corpo do pedido.

## <a name="prerequisites"></a>Pré-requisitos

* [Go 1.10.1](https://golang.org/dl/)
* Tem de ter um [serviço Criador de FAQ](../How-To/set-up-qnamaker-service-azure.md). Para recuperar a sua chave e ponto final (que inclui o nome do recurso), selecione **Quickstart** para o seu recurso no portal Azure.

* QnA Maker base de conhecimento (KB) ID encontrado no URL no parâmetro da `kbid` cadeia de consulta, como mostrado abaixo.

    ![ID da base de dados de conhecimento do Criador de FAQ](../media/qnamaker-quickstart-kb/qna-maker-id.png)

    Se ainda não tiver uma base de dados de conhecimento, pode criar uma de exemplo para utilizar neste guia de introdução: [Criar uma nova base de dados de conhecimento](create-new-kb-csharp.md).

> [!NOTE]
> Os ficheiros de solução completos estão disponíveis no [repositório GitHub **Azure-Samples/cognitive-services-qnamaker-go** GitHub](https://github.com/Azure-Samples/cognitive-services-qnamaker-go/tree/master/documentation-samples/quickstarts/publish-knowledge-base).

## <a name="create-a-go-file"></a>Criar um ficheiro Go

Abra o VSCode e crie um novo ficheiro chamado `publish-kb.go` .

## <a name="add-the-required-dependencies"></a>Adicionar as dependências necessárias

Na parte superior do `publish-kb.go`, adicione as seguintes linhas para adicionar as dependências necessárias ao projeto:

:::code language="go" source="~/cognitive-services-quickstart-code/go/QnAMaker/rest/publish-kb.go" id="dependencies":::

## <a name="create-the-main-function"></a>Criar a função principal

Após as dependências exigidas, adicione a seguinte classe:

```Go
package main

func main() {

}
```

## <a name="add-post-request-to-publish-kb"></a>Adicionar pedido POST para publicar KB

Adicione o seguinte código, que faz um pedido HTTPS à API do Fabricante QnA para publicar uma base de conhecimento e recebe a resposta:

:::code language="go" source="~/cognitive-services-quickstart-code/go/QnAMaker/rest/publish-kb.go" id="main":::

A chamada à API devolve um estado 204 para uma publicação com êxito, sem qualquer conteúdo no corpo da resposta. O código adiciona conteúdo para respostas 204.

Para qualquer outra resposta, a resposta é devolvida inalterada.

## <a name="build-and-run-the-program"></a>Compilar e executar o programa

Introduza o seguinte comando para compilar o ficheiro. A linha de comandos não devolve informações para uma compilação bem-sucedida.

```bash
go build publish-kb.go
```

Introduza o seguinte comando numa linha de comandos para executar o programa. Enviará o pedido à API do Fabricante QnA para publicar o KB e, em seguida, imprimirá 204 para sucesso ou erros.

```bash
./publish-kb
```

[!INCLUDE [Clean up files and knowledge base](../../../../includes/cognitive-services-qnamaker-quickstart-cleanup-resources.md)]

## <a name="next-steps"></a>Passos seguintes

Após a publicação da base de conhecimento, precisa do URL do [ponto final para gerar uma resposta.](./get-answer-from-knowledge-base-go.md)

> [!div class="nextstepaction"]
> [Referência à API REST do Criador de FAQ](/rest/api/cognitiveservices/qnamaker4.0/knowledgebase)