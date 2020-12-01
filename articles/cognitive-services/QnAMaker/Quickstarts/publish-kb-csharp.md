---
title: 'Quickstart: Publicar base de conhecimento, REST, C# - QnA Maker'
description: Este quickstart baseado em C# REST publica a sua base de conhecimento e cria um ponto final que pode ser chamado na sua aplicação ou chat bot.
ms.service: cognitive-services
ms.subservice: qna-maker
ms.date: 02/08/2020
ROBOTS: NOINDEX,NOFOLLOW
ms.custom: RESTCURL2020FEB27, devx-track-csharp
ms.topic: how-to
ms.openlocfilehash: abaccfb1a1f36e1f7f47d3f5acefb08e2091cb39
ms.sourcegitcommit: 9eda79ea41c60d58a4ceab63d424d6866b38b82d
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 11/30/2020
ms.locfileid: "96351185"
---
# <a name="quickstart-publish-a-knowledge-base-in-qna-maker-using-c"></a>Início Rápido: publicar uma base de dados de conhecimento no Criador de FAQ com o C#

Este quickstart baseado em REST acompanha-o através da publicação programática da sua base de conhecimento (KB). A publicação empurra a versão mais recente da base de conhecimento para um índice dedicado de Pesquisa Cognitiva Azure e cria um ponto final que pode ser chamado na sua aplicação ou chat bot.

Este início rápido chama as API do Criador de FAQ:
* [Publicar](/rest/api/cognitiveservices/qnamaker/knowledgebase/publish) - esta API não requer quaisquer informações no corpo do pedido.

## <a name="prerequisites"></a>Pré-requisitos

* A [**edição da Comunidade do Visual Studio**](https://www.visualstudio.com/downloads/) mais recente.
* Tem de ter um [serviço Criador de FAQ](../How-To/set-up-qnamaker-service-azure.md). Para recuperar a sua chave e ponto final (que inclui o nome do recurso), selecione **Quickstart** para o seu recurso no portal Azure.
* QnA Maker base de conhecimento (KB) ID encontrado no URL no parâmetro da `kbid` cadeia de consulta, como mostrado abaixo.

    ![ID da base de dados de conhecimento do Criador de FAQ](../media/qnamaker-quickstart-kb/qna-maker-id.png)

    Se ainda não tiver uma base de dados de conhecimento, pode criar uma de exemplo para utilizar neste guia de introdução: [Criar uma nova base de dados de conhecimento](create-new-kb-csharp.md).

> [!NOTE]
> Os ficheiros de solução completos estão disponíveis no [repositório **Azure-Samples/cognitive-services-qnamaker-csharp** GitHub](https://github.com/Azure-Samples/cognitive-services-qnamaker-csharp/tree/master/documentation-samples/quickstarts/publish-knowledge-base).

## <a name="create-knowledge-base-project"></a>Criar um projeto de base de dados de conhecimento

1. Edição comunitária Open Visual Studio 2019.
1. Crie um novo projeto **de App de Consola (.NET Core)** e nomeie o `QnaMakerQuickstart` projeto. Aceite as predefinições nas restantes definições.

## <a name="add-required-dependencies"></a>Adicionar as dependências necessárias

Na parte superior de Program.cs, substitua a instrução using única pelas seguintes linhas para adicionar as dependências necessárias ao projeto:

:::code language="csharp" source="~/cognitive-services-quickstart-code/dotnet/QnAMaker/rest/publish-kb.cs" id="dependencies":::

## <a name="add-required-constants"></a>Adicionar constantes necessárias

Na classe **Programa,** adicione as constantes necessárias para aceder ao QnA Maker.

:::code language="csharp" source="~/cognitive-services-quickstart-code/dotnet/QnAMaker/rest/publish-kb.cs" id="constants":::

## <a name="add-the-main-method-to-publish-the-knowledge-base"></a>Adicione o método principal para publicar a base de conhecimento

Após as constantes necessárias, adicione o seguinte código, que faz um pedido HTTPS à API do Fabricante QnA para publicar uma base de conhecimento e recebe a resposta:

:::code language="csharp" source="~/cognitive-services-quickstart-code/dotnet/QnAMaker/rest/publish-kb.cs" id="post":::

A chamada à API devolve um estado 204 para uma publicação com êxito, sem qualquer conteúdo no corpo da resposta.

## <a name="build-and-run-the-program"></a>Compilar e executar o programa

Crie e execute o programa. Enviará automaticamente o pedido à API do Criador de QnA para publicar a base de conhecimento, e a resposta é impressa na janela da consola.

Assim que a sua base de dados de conhecimento for publicada, pode consultá-la a partir do ponto final com uma aplicação cliente ou um chatbot.

[!INCLUDE [Clean up files and knowledge base](../../../../includes/cognitive-services-qnamaker-quickstart-cleanup-resources.md)]

## <a name="next-steps"></a>Passos seguintes

Após a publicação da base de conhecimento, precisa do URL do [ponto final para gerar uma resposta.](./get-answer-from-knowledge-base-csharp.md)

> [!div class="nextstepaction"]
> [Referência à API REST do Criador de FAQ](/rest/api/cognitiveservices/qnamaker4.0/knowledgebase)