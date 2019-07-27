---
title: 'Início rápido: Publicar a base de dados de conhecimento, REST, Node. js-QnA Maker'
titleSuffix: Azure Cognitive Services
description: Este guia de introdução do node. js explica-lhe publicar por meio de programação de sua base de dados de conhecimento (KB). A publicação emite a versão mais recente da base de dados de conhecimento para um índice dedicado do Azure Search e cria um ponto final que pode ser chamado na sua aplicação ou chatbot.
services: cognitive-services
author: diberry
manager: nitinme
ms.custom: seodec18
ms.service: cognitive-services
ms.subservice: qna-maker
ms.topic: quickstart
ms.date: 02/28/2019
ms.author: diberry
ms.openlocfilehash: d47359f92a2cebef10514b3746fbf32f7e952132
ms.sourcegitcommit: 7c4de3e22b8e9d71c579f31cbfcea9f22d43721a
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 07/26/2019
ms.locfileid: "68562910"
---
# <a name="quickstart-publish-a-knowledge-base-in-qna-maker-using-nodejs"></a>Início rápido: Publicar uma base de dados de conhecimento no QnA Maker usando o Node. js

Este guia de introdução baseada em REST orienta-o através de publicação por meio de programação de sua base de dados de conhecimento (KB). A publicação emite a versão mais recente da base de dados de conhecimento para um índice dedicado do Azure Search e cria um ponto final que pode ser chamado na sua aplicação ou chatbot.

Este início rápido chama as APIs do Criador de FAQ:
* [Publicar](https://docs.microsoft.com/rest/api/cognitiveservices/qnamaker/knowledgebase/publish) - esta API não requer quaisquer informações no corpo do pedido.

## <a name="prerequisites"></a>Pré-requisitos

* [Node.js 6+](https://nodejs.org/en/download/)
* Tem de ter um [serviço Criador de FAQ](../How-To/set-up-qnamaker-service-azure.md). Para obter a sua chave, selecione **Chaves** em **Gestão de Recursos** no seu dashboard. 
* O ID da base de dados de conhecimento (KB) do Criador de FAQ encontrado no URL no parâmetro de cadeia de consulta kbid, conforme mostrado abaixo.

    ![ID da base de dados de conhecimento do Criador de FAQ](../media/qnamaker-quickstart-kb/qna-maker-id.png)

    Se você ainda não tiver uma base de dados de conhecimento, poderá criar uma amostra a ser usada para este guia de início rápido: [Crie uma nova base de dados de conhecimento](create-new-kb-nodejs.md).


> [!NOTE] 
> Os arquivos de solução completos estão disponíveis no [repositório GitHub **Azure-Samples/cognitiva-Services-qnamaker-NodeJS** ](https://github.com/Azure-Samples/cognitive-services-qnamaker-nodejs/tree/master/documentation-samples/quickstarts/publish-knowledge-base-short).

## <a name="create-a-knowledge-base-nodejs-file"></a>Criar um ficheiro Node.js de base de dados de conhecimento

Crie um ficheiro com o nome `publish-knowledge-base.js`.

## <a name="add-required-dependencies"></a>Adicionar as dependências necessárias

Na parte superior do `publish-knowledge-base.js`, adicione as seguintes linhas para adicionar as dependências necessárias ao projeto:

[!code-nodejs[Add the dependencies](~/samples-qnamaker-nodejs/documentation-samples/quickstarts/publish-knowledge-base-short/publish-knowledge-base.js?range=1-3 "Add the dependencies")]

## <a name="add-required-constants"></a>Adicionar constantes necessárias

Depois das dependências necessárias anteriores, adicione as constantes necessárias para aceder ao Criador de FAQ. Substitua os valores pelos seus próprios.

[!code-nodejs[Add required constants](~/samples-qnamaker-nodejs/documentation-samples/quickstarts/publish-knowledge-base-short/publish-knowledge-base.js?range=11-14 "Add required constants")]

## <a name="add-post-request-to-publish-knowledge-base"></a>Adicionar pedido POST para publicar a base de dados de conhecimento

Depois das constantes necessárias, adicione o seguinte código, o que faz um pedido HTTPS para a API do QnA Maker para publicar uma base de dados de conhecimento e recebe a resposta:

[!code-nodejs[Add a POST request to publish knowledge base](~/samples-qnamaker-nodejs/documentation-samples/quickstarts/publish-knowledge-base-short/publish-knowledge-base.js?range=16-47 "Add a POST request to publish knowledge base")]

A chamada à API devolve um estado 204 para uma publicação com êxito, sem qualquer conteúdo no corpo da resposta. O código adiciona conteúdo para respostas 204.

Para qualquer outra resposta, a resposta é devolvida inalterada.

## <a name="run-the-program"></a>Execute o programa

Compile e execute o programa. Ele enviará automaticamente o pedido para a API do QnA Maker para publicar a base de dados de conhecimento, em seguida, a resposta é impresso para a janela da consola.

Assim que a sua base de dados de conhecimento for publicada, pode consultá-la a partir do ponto final com uma aplicação cliente ou um chatbot. 

```bash
node publish-knowledge-base.js
```

[!INCLUDE [Clean up files and knowledge base](../../../../includes/cognitive-services-qnamaker-quickstart-cleanup-resources.md)] 

## <a name="next-steps"></a>Passos Seguintes

Depois da base de dados de conhecimento é publicado, é necessário o [URL de ponto final para gerar uma resposta](../Tutorials/create-publish-answer.md#generating-an-answer). 

> [!div class="nextstepaction"]
> [Referência à API REST do Criador de FAQ](https://go.microsoft.com/fwlink/?linkid=2092179)
