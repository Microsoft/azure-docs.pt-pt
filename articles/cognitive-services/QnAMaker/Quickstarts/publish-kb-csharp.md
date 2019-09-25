---
title: 'Início rápido: Publicar base de dados de conhecimento C# , REST QnA Maker'
titleSuffix: Azure Cognitive Services
description: Isso C# baseado em REST início rápido explica-lhe publicar a sua base de dados de conhecimento que envia a versão mais recente da base de dados de conhecimento testada para um índice da Azure Search dedicado que representa a base de dados de conhecimento publicada. Também cria um ponto final que pode ser chamado na sua aplicação ou chatbot.
services: cognitive-services
author: diberry
manager: nitinme
ms.custom: seodec18
ms.service: cognitive-services
ms.subservice: qna-maker
ms.topic: quickstart
ms.date: 09/24/2019
ms.author: diberry
ms.openlocfilehash: a116dd595b7bde3f320bc0617c99fdb2ab7830d4
ms.sourcegitcommit: 55f7fc8fe5f6d874d5e886cb014e2070f49f3b94
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 09/25/2019
ms.locfileid: "71261961"
---
# <a name="quickstart-publish-a-knowledge-base-in-qna-maker-using-c"></a>Início rápido: Publicar uma base de dados de conhecimento no QnA Maker usandoC#

Este guia de introdução baseada em REST orienta-o através de publicação por meio de programação de sua base de dados de conhecimento (KB). A publicação emite a versão mais recente da base de dados de conhecimento para um índice dedicado do Azure Search e cria um ponto final que pode ser chamado na sua aplicação ou chatbot.

Este início rápido chama as APIs do Criador de FAQ:
* [Publicar](https://docs.microsoft.com/rest/api/cognitiveservices/qnamaker/knowledgebase/publish) - esta API não requer quaisquer informações no corpo do pedido.

## <a name="prerequisites"></a>Pré-requisitos

* A [**edição da Comunidade do Visual Studio**](https://www.visualstudio.com/downloads/) mais recente.
* Tem de ter um [serviço Criador de FAQ](../How-To/set-up-qnamaker-service-azure.md). Para obter a sua chave, selecione **Chaves** em **Gestão de Recursos** no seu dashboard. 
* O ID da base de dados de conhecimento (KB) do Criador de FAQ encontrado no URL no parâmetro de cadeia de consulta kbid, conforme mostrado abaixo.

    ![ID da base de dados de conhecimento do Criador de FAQ](../media/qnamaker-quickstart-kb/qna-maker-id.png)

    Se você ainda não tiver uma base de dados de conhecimento, poderá criar uma amostra a ser usada para este guia de início rápido: [Crie uma nova base de dados de conhecimento](create-new-kb-csharp.md).

> [!NOTE] 
> Os arquivos de solução completos estão disponíveis no repositório do GitHub [ **Azure-Samples/cognitiva-Services-qnamaker-Csharp** ](https://github.com/Azure-Samples/cognitive-services-qnamaker-csharp/tree/master/documentation-samples/quickstarts/publish-knowledge-base).

## <a name="create-knowledge-base-project"></a>Criar um projeto de base de dados de conhecimento

1. Abra o Visual Studio 2019 Community Edition.
1. Crie um novo projeto de **aplicativo de console (.NET Core)** e nomeie `QnaMakerQuickstart`o projeto. Aceite as predefinições nas restantes definições.

## <a name="add-required-dependencies"></a>Adicionar as dependências necessárias

Na parte superior de Program.cs, substitua a instrução using única pelas seguintes linhas para adicionar as dependências necessárias ao projeto:

[!code-csharp[Add the required dependencies](~/samples-qnamaker-csharp/documentation-samples/quickstarts/publish-knowledge-base/QnAMakerPublishQuickstart/Program.cs?range=1-2 "Add the required dependencies")]

## <a name="add-required-constants"></a>Adicionar constantes necessárias

Na classe **programa** , adicione as constantes necessárias para acessar QnA Maker.

[!code-csharp[Add the required constants](~/samples-qnamaker-csharp/documentation-samples/quickstarts/publish-knowledge-base/QnAMakerPublishQuickstart/Program.cs?range=8-34 "Add the required constants")]

## <a name="add-the-main-method-to-publish-the-knowledge-base"></a>Adicionar o método Main para publicar a base de dados de conhecimento

Depois das constantes necessárias, adicione o seguinte código, o que faz um pedido HTTPS para a API do QnA Maker para publicar uma base de dados de conhecimento e recebe a resposta:

[!code-csharp[Add HTTP Post request and response](~/samples-qnamaker-csharp/documentation-samples/quickstarts/publish-knowledge-base/QnAMakerPublishQuickstart/Program.cs?range=36-56 "Add HTTP Post request and response")]

A chamada à API devolve um estado 204 para uma publicação com êxito, sem qualquer conteúdo no corpo da resposta. 
 
## <a name="build-and-run-the-program"></a>Compilar e executar o programa

Compile e execute o programa. Ele enviará automaticamente o pedido para a API do QnA Maker para publicar a base de dados de conhecimento, em seguida, a resposta é impresso para a janela da consola.

Assim que a sua base de dados de conhecimento for publicada, pode consultá-la a partir do ponto final com uma aplicação cliente ou um chatbot. 

[!INCLUDE [Clean up files and knowledge base](../../../../includes/cognitive-services-qnamaker-quickstart-cleanup-resources.md)] 

## <a name="next-steps"></a>Passos Seguintes

Depois da base de dados de conhecimento é publicado, é necessário o [URL de ponto final para gerar uma resposta](../Tutorials/create-publish-answer.md#generating-an-answer). 

> [!div class="nextstepaction"]
> [Referência à API REST do Criador de FAQ](https://go.microsoft.com/fwlink/?linkid=2092179)
