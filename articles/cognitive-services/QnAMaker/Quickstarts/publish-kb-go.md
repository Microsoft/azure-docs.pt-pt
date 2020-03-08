---
title: 'Quickstart: Publicar base de conhecimento, REST, Go - QnA Maker'
description: Este quickstart baseado em Go REST publica a sua base de conhecimentos e cria um ponto final que pode ser chamado na sua aplicação ou chat bot.
ms.date: 02/08/2020
ROBOTS: NOINDEX,NOFOLLOW
ms.custom: RESTCURL2020FEB27
ms.topic: conceptual
ms.openlocfilehash: 4ce655bdc7a913ecb281ce8a75e7ec4f2009a2ea
ms.sourcegitcommit: f5e4d0466b417fa511b942fd3bd206aeae0055bc
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/06/2020
ms.locfileid: "78851698"
---
# <a name="quickstart-publish-a-knowledge-base-in-qna-maker-using-go"></a>Início Rápido: publicar uma base de dados de conhecimento no Criador de FAQ com o Go

Este guia de introdução baseada em REST orienta-o através de publicação por meio de programação de sua base de dados de conhecimento (KB). A publicação empurra a versão mais recente da base de conhecimento para um índice dedicado de Pesquisa Cognitiva Azure e cria um ponto final que pode ser chamado na sua aplicação ou chat bot.

Este início rápido chama as APIs do Criador de FAQ:
* [Publicar](https://docs.microsoft.com/rest/api/cognitiveservices/qnamaker/knowledgebase/publish) - esta API não requer quaisquer informações no corpo do pedido.

## <a name="prerequisites"></a>Pré-requisitos

* [Go 1.10.1](https://golang.org/dl/)
* Tem de ter um [serviço Criador de FAQ](../How-To/set-up-qnamaker-service-azure.md). Para recuperar a sua chave e ponto final (que inclui o nome do recurso), selecione **Quickstart** para o seu recurso no portal Azure.

* QnA Maker base de conhecimento (KB) ID encontrado no URL no parâmetro de corda de consulta `kbid`, como mostrado abaixo.

    ![ID da base de dados de conhecimento do Criador de FAQ](../media/qnamaker-quickstart-kb/qna-maker-id.png)

    Se ainda não tiver uma base de dados de conhecimento, pode criar uma de exemplo para utilizar neste guia de introdução: [Criar uma nova base de dados de conhecimento](create-new-kb-csharp.md).

> [!NOTE]
> Os ficheiros completos de solução estão disponíveis no [repositório **Azure-Samples/cognitive-services-qnamaker-go** GitHub](https://github.com/Azure-Samples/cognitive-services-qnamaker-go/tree/master/documentation-samples/quickstarts/publish-knowledge-base).

## <a name="create-a-go-file"></a>Crie um ficheiro de Go

Abra o VSCode e crie um novo ficheiro chamado `publish-kb.go`.

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

## <a name="add-required-constants"></a>Adicionar as constantes necessárias

Dentro da **principal**


 função, adicione as constantes necessárias para acessar o QnA Maker. Substitua os valores pelos seus próprios.

[!code-go[Add the required constants](~/samples-qnamaker-go/documentation-samples/quickstarts/publish-knowledge-base/publish-kb.go?range=16-20 "Add the required constants")]

## <a name="add-post-request-to-publish-kb"></a>Adicionar pedido POST para publicar KB

Depois das constantes necessárias, adicione o seguinte código, o que faz um pedido HTTPS para a API do QnA Maker para publicar uma base de dados de conhecimento e recebe a resposta:

[!code-go[Add a POST request to publish KB](~/samples-qnamaker-go/documentation-samples/quickstarts/get-answer/get-answer.go?range=35-48 "Add a POST request to publish KB")]

A chamada à API devolve um estado 204 para uma publicação com êxito, sem qualquer conteúdo no corpo da resposta. O código adiciona conteúdo para respostas 204.

Para qualquer outra resposta, a resposta é devolvida inalterada.

## <a name="build-and-run-the-program"></a>Criar e executar o programa

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

Após a publicação da base de conhecimentos, é necessário que o [URL do ponto final gere uma resposta](./get-answer-from-knowledge-base-go.md).

> [!div class="nextstepaction"]
> [Referência da API REST para o Criador de FAQ (V4)](https://go.microsoft.com/fwlink/?linkid=2092179)