---
title: 'Início rápido: Obter resposta da base de dados de conhecimento C# – REST,-QnA Maker'
titleSuffix: Azure Cognitive Services
description: Este C# guia de início rápido baseado em REST orienta você pela obtenção de uma resposta de uma base de dados de conhecimento, programaticamente.
services: cognitive-services
author: diberry
manager: nitinme
ms.service: cognitive-services
ms.subservice: qna-maker
ms.topic: quickstart
ms.date: 10/01/2019
ms.author: diberry
ms.openlocfilehash: e06ded1b684d442c9f6257ebd92b3a3b8fdb87a3
ms.sourcegitcommit: 4f3f502447ca8ea9b932b8b7402ce557f21ebe5a
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/02/2019
ms.locfileid: "71803011"
---
# <a name="quickstart-get-answers-to-a-question-from-a-knowledge-base-with-c"></a>Início rápido: Obtenha respostas para uma pergunta de uma base de dados de conhecimento comC#

Este guia de início rápido orienta você na obtenção de uma resposta de uma base de dados de conhecimento QnA Maker publicada. A base de conhecimento contém perguntas e respostas de [fontes de dados](../Concepts/data-sources-supported.md) , como perguntas frequentes. A [pergunta](../how-to/metadata-generateanswer-usage.md#generateanswer-request-configuration) é enviada para o serviço de QnA Maker. A [resposta](../how-to/metadata-generateanswer-usage.md#generateanswer-response-properties) inclui a resposta mais prevista. 


## <a name="prerequisites"></a>Pré-requisitos

* A [**edição da Comunidade do Visual Studio**](https://www.visualstudio.com/downloads/) mais recente.
* Tem de ter um [serviço Criador de FAQ](../How-To/set-up-qnamaker-service-azure.md). Para obter a sua chave, selecione **chaves** sob **gestão de recursos** no dashboard do Azure para o seu recurso do QnA Maker. 
* **Publicar** configurações da página. Se você não tiver uma base de dados de conhecimento publicada, crie uma base de dados de conhecimento vazia, importe uma base de dados de conhecimento na página **configurações** e, em seguida, publique. Você pode baixar e usar [essa base de dados de conhecimento básica](https://github.com/Azure-Samples/cognitive-services-sample-data-files/blob/master/qna-maker/knowledge-bases/basic-kb.tsv). 

    As configurações da página de publicação incluem o valor da rota de POST, o valor do host e o valor de EndpointKey. 

    ![Definições de publicação](../media/qnamaker-quickstart-get-answer/publish-settings.png)

O código para este guia de início rápido [https://github.com/Azure-Samples/cognitive-services-qnamaker-csharp](https://github.com/Azure-Samples/cognitive-services-qnamaker-csharp/tree/master/documentation-samples/quickstarts/get-answer) está no repositório. 

## <a name="create-a-knowledge-base-project"></a>Criar um projeto de base de dados de conhecimento

1. Abra o Visual Studio 2019 Community Edition.
1. Crie um novo projeto de aplicativo de console (.NET Core) e nomeie o projeto QnaMakerQuickstart. Aceite as predefinições nas restantes definições.

## <a name="add-the-required-dependencies"></a>Adicionar as dependências necessárias

Na parte superior do arquivo Program.cs, substitua a única instrução using pelas linhas a seguir para adicionar as dependências necessárias ao projeto:

[!code-csharp[Add the required dependencies](~/samples-qnamaker-csharp/documentation-samples/quickstarts/get-answer/QnAMakerAnswerQuestion/Program.cs?range=1-3 "Add the required dependencies")]

## <a name="add-the-required-constants"></a>Adicionar as constantes necessárias

Na parte superior da `Program` classe, dentro do `Main`, adicione as constantes necessárias para acessar QnA Maker. Esses valores estão na página **publicar** depois que você publicar a base de dados de conhecimento. 

[!code-csharp[Add the required constants](~/samples-qnamaker-csharp/documentation-samples/quickstarts/get-answer/QnAMakerAnswerQuestion/Program.cs?range=9-41 "Add the required constants")]

## <a name="add-a-post-request-to-send-question-and-get-answer"></a>Adicionar uma solicitação POST para enviar pergunta e obter resposta

O código a seguir faz uma solicitação HTTPS para o API de QnA Maker para enviar a pergunta para a base de dados de conhecimento e recebe a resposta:

[!code-csharp[Add a POST request to send question to knowledge base](~/samples-qnamaker-csharp/documentation-samples/quickstarts/get-answer/QnAMakerAnswerQuestion/Program.cs?range=43-76 "Add a POST request to send question to knowledge base")]

O `Authorization` valor do cabeçalho inclui a cadeia `EndpointKey`de caracteres. 

Saiba mais sobre a [solicitação](../how-to/metadata-generateanswer-usage.md#generateanswer-request) e a [resposta](../how-to/metadata-generateanswer-usage.md#generateanswer-response). 

## <a name="build-and-run-the-program"></a>Compilar e executar o programa

Compile e execute o programa a partir do Visual Studio. Ele enviará automaticamente o pedido para a API QnA Maker, em seguida, será impresso para a janela da consola.

[!INCLUDE [JSON request and response](../../../../includes/cognitive-services-qnamaker-quickstart-get-answer-json.md)] 

[!INCLUDE [Clean up files and knowledge base](../../../../includes/cognitive-services-qnamaker-quickstart-cleanup-resources.md)] 

## <a name="next-steps"></a>Passos seguintes

> [!div class="nextstepaction"]
> [Referência à API REST do Criador de FAQ](https://go.microsoft.com/fwlink/?linkid=2092179)
