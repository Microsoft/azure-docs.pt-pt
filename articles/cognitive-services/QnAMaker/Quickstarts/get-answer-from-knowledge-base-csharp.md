---
title: 'Quickstart: Obtenha resposta da base C# de conhecimento - REST, - QnA Maker'
description: Este C# quickstart baseado em REST leva-o através de obter uma resposta de uma base de conhecimento, programáticamente.
ms.date: 02/08/2020
ROBOTS: NOINDEX,NOFOLLOW
ms.custom: RESTCURL2020FEB27
ms.topic: conceptual
ms.openlocfilehash: 16093ec5e837b098da3c9b038fe2a57cd76c7151
ms.sourcegitcommit: f5e4d0466b417fa511b942fd3bd206aeae0055bc
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/06/2020
ms.locfileid: "78851808"
---
# <a name="quickstart-get-answers-to-a-question-from-a-knowledge-base-with-c"></a>Quickstart: Obtenha respostas para uma pergunta de uma base de conhecimento comC#

Este quickstart leva-o programáticamente a obter uma resposta de uma base de conhecimento publicada da QnA Maker. A base de conhecimento contém perguntas e respostas de [fontes](../Concepts/knowledge-base.md) de dados como FAQs. A [questão](../how-to/metadata-generateanswer-usage.md#generateanswer-request-configuration) é enviada ao serviço QnA Maker. A [resposta](../how-to/metadata-generateanswer-usage.md#generateanswer-response-properties) inclui a resposta mais bem prevista.

[Documentação de referência](https://docs.microsoft.com/rest/api/cognitiveservices/qnamakerruntime/runtime) | [Amostra](https://github.com/Azure-Samples/cognitive-services-qnamaker-csharp/blob/master/documentation-samples/quickstarts/get-answer/QnAMakerAnswerQuestion/Program.cs)

## <a name="prerequisites"></a>Pré-requisitos

* A [**edição da Comunidade do Visual Studio**](https://www.visualstudio.com/downloads/) mais recente.
* Tem de ter um [serviço Criador de FAQ](../How-To/set-up-qnamaker-service-azure.md). Para recuperar a sua chave, selecione **Keys** sob **Gestão** de Recursos no seu painel Azure para o seu recurso QnA Maker.
* **Publique** as definições da página. Se não tiver uma base de conhecimento publicada, crie uma base de conhecimento vazia e, em seguida, importe uma base de conhecimento na página **Definições** e, em seguida, publique. Pode descarregar e utilizar [esta base de conhecimentos básicos.](https://github.com/Azure-Samples/cognitive-services-sample-data-files/blob/master/qna-maker/knowledge-bases/basic-kb.tsv)

    As definições da página de publicação incluem o valor da rota POST, o valor do anfitrião e o valor EndpointKey.

    ![Definições de publicação](../media/qnamaker-quickstart-get-answer/publish-settings.png)

## <a name="create-a-knowledge-base-project"></a>Criar um projeto de base de dados de conhecimento

1. Edição comunitária open Visual Studio 2019.
1. Crie um novo projeto de Consola App (.NET Core) e nomeie o projeto QnaMakerQuickstart. Aceite as predefinições nas restantes definições.

## <a name="add-the-required-dependencies"></a>Adicionar as dependências necessárias

No topo do ficheiro Program.cs, substitua a única declaração utilizando as seguintes linhas para adicionar dependências necessárias ao projeto:

[!code-csharp[Add the required dependencies](~/samples-qnamaker-csharp/documentation-samples/quickstarts/get-answer/QnAMakerAnswerQuestion/Program.cs?range=1-3 "Add the required dependencies")]

## <a name="add-the-required-constants"></a>Adicionar as constantes necessárias

No topo da classe `Program`, dentro do `Main`, adicione as constantes necessárias para aceder ao QnA Maker. Estes valores estão na página **Publicar** depois de publicar a base de conhecimento.

[!code-csharp[Add the required constants](~/samples-qnamaker-csharp/documentation-samples/quickstarts/get-answer/QnAMakerAnswerQuestion/Program.cs?range=9-41 "Add the required constants")]

## <a name="add-a-post-request-to-send-question-and-get-answer"></a>Adicione um pedido post para enviar pergunta e obter resposta

O seguinte código faz um pedido HTTPS à API do Fabricante de QnA para enviar a questão para a base de conhecimento e recebe a resposta:

[!code-csharp[Add a POST request to send question to knowledge base](~/samples-qnamaker-csharp/documentation-samples/quickstarts/get-answer/QnAMakerAnswerQuestion/Program.cs?range=43-76 "Add a POST request to send question to knowledge base")]

O valor do cabeçalho `Authorization` inclui a cadeia `EndpointKey`.

Saiba mais sobre o [pedido](../how-to/metadata-generateanswer-usage.md#generateanswer-request) e [resposta.](../how-to/metadata-generateanswer-usage.md#generateanswer-response)

## <a name="build-and-run-the-program"></a>Criar e executar o programa

Construa e execute o programa a partir do Estúdio Visual. Ele enviará automaticamente o pedido para a API QnA Maker, em seguida, será impresso para a janela da consola.

[!INCLUDE [JSON request and response](../../../../includes/cognitive-services-qnamaker-quickstart-get-answer-json.md)]

[!INCLUDE [Clean up files and knowledge base](../../../../includes/cognitive-services-qnamaker-quickstart-cleanup-resources.md)]

## <a name="next-steps"></a>Passos seguintes

> [!div class="nextstepaction"]
> [Referência da API REST para o Criador de FAQ (V4)](https://go.microsoft.com/fwlink/?linkid=2092179)