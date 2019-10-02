---
title: 'Início rápido: Obter resposta da base de dados de conhecimento-REST, Node. js-QnA Maker'
titleSuffix: Azure Cognitive Services
description: Este guia de início rápido baseado em REST do node. js orienta você pela obtenção de uma resposta de uma base de dados de conhecimento, programaticamente.
services: cognitive-services
author: diberry
manager: nitinme
ms.service: cognitive-services
ms.subservice: qna-maker
ms.topic: quickstart
ms.date: 10/01/2019
ms.author: diberry
ms.openlocfilehash: 8ed5225591065082d937f1cac92893886bf90e40
ms.sourcegitcommit: 4f3f502447ca8ea9b932b8b7402ce557f21ebe5a
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/02/2019
ms.locfileid: "71802933"
---
# <a name="quickstart-get-answers-to-a-question-from-a-knowledge-base-with-nodejs"></a>Início rápido: Obtenha respostas para uma pergunta de uma base de dados de conhecimento com o Node. js

Este guia de início rápido orienta você na obtenção de uma resposta de uma base de dados de conhecimento QnA Maker publicada. A base de conhecimento contém perguntas e respostas de [fontes de dados](../Concepts/data-sources-supported.md) , como perguntas frequentes. A [pergunta](../how-to/metadata-generateanswer-usage.md#generateanswer-request-configuration) é enviada para o serviço de QnA Maker. A [resposta](../how-to/metadata-generateanswer-usage.md#generateanswer-response-properties) inclui a resposta mais prevista. 

## <a name="prerequisites"></a>Pré-requisitos

* [Node.js](https://nodejs.org/en/download/)
* [Visual Studio Code](https://code.visualstudio.com/)
* Tem de ter um [serviço Criador de FAQ](../How-To/set-up-qnamaker-service-azure.md). Para obter a sua chave, selecione **chaves** sob **gestão de recursos** no dashboard do Azure para o seu recurso do QnA Maker. 
* **Publicar** configurações da página. Se você não tiver uma base de dados de conhecimento publicada, crie uma base de dados de conhecimento vazia, importe uma base de dados de conhecimento na página **configurações** e, em seguida, publique. Você pode baixar e usar [essa base de dados de conhecimento básica](https://github.com/Azure-Samples/cognitive-services-sample-data-files/blob/master/qna-maker/knowledge-bases/basic-kb.tsv). 

    As configurações da página de publicação incluem o valor da rota de POST, o valor do host e o valor de EndpointKey. 

    ![Definições de publicação](../media/qnamaker-quickstart-get-answer/publish-settings.png)

O código para este guia de início rápido [https://github.com/Azure-Samples/cognitive-services-qnamaker-nodejs](https://github.com/Azure-Samples/cognitive-services-qnamaker-nodejs/tree/master/documentation-samples/quickstarts/get-answer) está no repositório. 

## <a name="create-a-nodejs-file"></a>Criar um arquivo node. js

Abra o VSCode e crie um novo ficheiro designado `get-answer.js`. 

## <a name="add-the-required-dependencies"></a>Adicionar as dependências necessárias

Na parte superior do `get-answer.js` arquivo, adicione as dependências necessárias ao projeto:

[!code-nodejs[Add the required dependencies](~/samples-qnamaker-nodejs/documentation-samples/quickstarts/get-answer/get-answer.js?range=1-4 "Add the required dependencies")]

## <a name="add-the-required-constants"></a>Adicionar as constantes necessárias

Em seguida, adicione as constantes necessárias para acessar QnA Maker. Esses valores estão na página **publicar** depois que você publicar a base de dados de conhecimento. 

[!code-nodejs[Add the required constants](~/samples-qnamaker-nodejs/documentation-samples/quickstarts/get-answer/get-answer.js?range=6-22 "Add the required constants")]

## <a name="add-a-post-request-to-send-question-and-get-an-answer"></a>Adicionar uma solicitação POST para enviar a pergunta e obter uma resposta

O código a seguir faz uma solicitação HTTPS para o API de QnA Maker para enviar a pergunta para a base de dados de conhecimento e recebe a resposta:

[!code-nodejs[Add a POST request to send question to knowledge base](~/samples-qnamaker-nodejs/documentation-samples/quickstarts/get-answer/get-answer.js?range=24-49 "Add a POST request to send question to knowledge base")]

O `Authorization` valor do cabeçalho inclui a cadeia `EndpointKey`de caracteres. 

## <a name="install-the-dependencies"></a>Instalar as dependências

Instale as dependências da linha de comando:

```bash
npm install request request-promise
```

## <a name="run-the-program"></a>Execute o programa

Execute o programa na linha de comando. Ele enviará automaticamente o pedido para a API QnA Maker, em seguida, será impresso para a janela da consola.

Execute o ficheiro:

```bash
node get-answer.js
```

[!INCLUDE [JSON request and response](../../../../includes/cognitive-services-qnamaker-quickstart-get-answer-json.md)]

Saiba mais sobre a [solicitação](../how-to/metadata-generateanswer-usage.md#generateanswer-request) e a [resposta](../how-to/metadata-generateanswer-usage.md#generateanswer-response).

[!INCLUDE [Clean up files and knowledge base](../../../../includes/cognitive-services-qnamaker-quickstart-cleanup-resources.md)] 

## <a name="next-steps"></a>Passos seguintes

> [!div class="nextstepaction"]
> [Referência à API REST do Criador de FAQ](https://go.microsoft.com/fwlink/?linkid=2092179)
