---
title: 'Início rápido: Receber resposta da base de dados de conhecimento - REST, C# -QnA Maker'
titlesuffix: Azure Cognitive Services
description: Isso C# baseado em REST início rápido explica-lhe obter uma resposta de uma base de dados de conhecimento, por meio de programação.
services: cognitive-services
author: diberry
manager: nitinme
ms.service: cognitive-services
ms.subservice: qna-maker
ms.topic: quickstart
ms.date: 02/28/2019
ms.author: diberry
ms.openlocfilehash: 2d011dffa4db68a0cdf6ac3415ff1316906a4e87
ms.sourcegitcommit: 5839af386c5a2ad46aaaeb90a13065ef94e61e74
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/19/2019
ms.locfileid: "57884006"
---
# <a name="get-answers-to-a-question-from-a-knowledge-base-with-c"></a>Obtenha respostas a uma pergunta de uma base de dados de conhecimento comC#

Neste início rápido explica como obter programaticamente uma resposta de uma base de dados de conhecimento publicada do QnA Maker. A base de dados de conhecimento contém perguntas e respostas partir [origens de dados](../Concepts/data-sources-supported.md) como FAQs. O [pergunta](../how-to/metadata-generateanswer-usage.md#generateanswer-request-configuration) é enviado para o serviço QnA Maker. O [resposta](../how-to/metadata-generateanswer-usage.md#generateanswer-response-properties) inclui a resposta previstos para parte superior. 


## <a name="prerequisites"></a>Pré-requisitos

* A [**edição da Comunidade do Visual Studio**](https://www.visualstudio.com/downloads/) mais recente.
* Tem de ter um [serviço Criador de FAQ](../How-To/set-up-qnamaker-service-azure.md). Para obter a sua chave, selecione **chaves** sob **gestão de recursos** no dashboard do Azure para o seu recurso do QnA Maker. 
* **Publicar** página Definições. Se não tiver uma base de dados de conhecimento publicada, criar uma base de dados de conhecimento vazia, em seguida, importar uma base de dados de conhecimento sobre o **definições** página, em seguida, publique. Pode transferir e utilizar [esta base de dados de conhecimento básico](https://github.com/Azure-Samples/cognitive-services-sample-data-files/blob/master/qna-maker/knowledge-bases/basic-kb.tsv). 

    As definições de página de publicação incluem o valor de rotas de publicação, o valor do anfitrião e o valor de EndpointKey. 

    ![Definições de publicação](../media/qnamaker-quickstart-get-answer/publish-settings.png)

O código para este início rápido está no [ https://github.com/Azure-Samples/cognitive-services-qnamaker-csharp ](https://github.com/Azure-Samples/cognitive-services-qnamaker-csharp/tree/master/documentation-samples/quickstarts/get-answer) repositório. 

## <a name="create-a-knowledge-base-project"></a>Criar um projeto de base de dados de conhecimento

1. Abra o Visual Studio 2017 Community Edition.
1. Crie um novo projeto de aplicação de consola (.NET Core) e o nome do projeto QnaMakerQuickstart. Aceite as predefinições nas restantes definições.

## <a name="add-the-required-dependencies"></a>Adicionar as dependências necessárias

Na parte superior do ficheiro Program.cs, substitua a única instrução using com as seguintes linhas para adicionar as dependências necessárias ao projeto:

[!code-csharp[Add the required dependencies](~/samples-qnamaker-csharp/documentation-samples/quickstarts/get-answer/QnAMakerAnswerQuestion/Program.cs?range=1-3 "Add the required dependencies")]

## <a name="add-the-required-constants"></a>Adicionar as constantes necessárias

Na parte superior a `Program` , no interior de classe a `Main`, adicione as constantes necessárias para acessar o QnA Maker. Estes valores são sobre o **publicar** página depois de publicar a base de dados de conhecimento. 

[!code-csharp[Add the required constants](~/samples-qnamaker-csharp/documentation-samples/quickstarts/get-answer/QnAMakerAnswerQuestion/Program.cs?range=14-30 "Add the required constants")]

## <a name="add-a-post-request-to-send-question-and-get-answer"></a>Adicionar um pedido POST para enviar a pergunta e obter resposta

O código a seguir faz um pedido HTTPS para a API do QnA Maker para enviar a pergunta para a base de dados de conhecimento e recebe a resposta:

[!code-csharp[Add a POST request to send question to knowledge base](~/samples-qnamaker-csharp/documentation-samples/quickstarts/get-answer/QnAMakerAnswerQuestion/Program.cs?range=32-57 "Add a POST request to send question to knowledge base")]

O `Authorization` valor do cabeçalho inclui a cadeia de caracteres `EndpointKey `. 

Saiba mais sobre o [solicitação](../how-to/metadata-generateanswer-usage.md#generateanswer-request) e [resposta](../how-to/metadata-generateanswer-usage.md#generateanswer-response). 

## <a name="build-and-run-the-program"></a>Compilar e executar o programa

Crie e execute o programa a partir do Visual Studio. Ele enviará automaticamente o pedido para a API QnA Maker, em seguida, será impresso para a janela da consola.

[!INCLUDE [JSON request and response](../../../../includes/cognitive-services-qnamaker-quickstart-get-answer-json.md)] 

[!INCLUDE [Clean up files and knowledge base](../../../../includes/cognitive-services-qnamaker-quickstart-cleanup-resources.md)] 

## <a name="next-steps"></a>Passos Seguintes

> [!div class="nextstepaction"]
> [Referência à API REST do Criador de FAQ](https://westus.dev.cognitive.microsoft.com/docs/services/5a93fcf85b4ccd136866eb37/operations/5ac266295b4ccd1554da75ff)
