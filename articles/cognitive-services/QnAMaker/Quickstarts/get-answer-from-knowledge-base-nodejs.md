---
title: 'Início rápido: Receber resposta da base de dados de conhecimento - REST, node. js - QnA Maker'
titlesuffix: Azure Cognitive Services
description: Este guia de introdução baseada em REST de node. js explica como obter uma resposta de uma base de dados de conhecimento, por meio de programação.
services: cognitive-services
author: diberry
manager: nitinme
ms.service: cognitive-services
ms.subservice: qna-maker
ms.topic: quickstart
ms.date: 02/28/2019
ms.author: diberry
ms.openlocfilehash: d9d8b76501932667a1c5adc93ab1e6ee7939ac7a
ms.sourcegitcommit: c712cb5c80bed4b5801be214788770b66bf7a009
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/01/2019
ms.locfileid: "57215931"
---
# <a name="get-answers-to-a-question-from-a-knowledge-base-with-nodejs"></a>Obtenha respostas a uma pergunta de uma base de dados de conhecimento com node. js

Neste início rápido explica como obter programaticamente uma resposta de uma base de dados de conhecimento publicada do QnA Maker. A base de dados de conhecimento contém perguntas e respostas partir [origens de dados](../Concepts/data-sources-supported.md) como FAQs. O [pergunta](../how-to/metadata-generateanswer-usage.md#generateanswer-request-configuration) é enviado para o serviço QnA Maker. O [resposta](../how-to/metadata-generateanswer-usage.md#generateanswer-response-properties) inclui a resposta previstos para parte superior. 

## <a name="prerequisites"></a>Pré-requisitos

* [Node.js](https://nodejs.org/en/download/)
* [Visual Studio Code](https://code.visualstudio.com/)
* Tem de ter um [serviço Criador de FAQ](../How-To/set-up-qnamaker-service-azure.md). Para obter a sua chave, selecione **chaves** sob **gestão de recursos** no dashboard do Azure para o seu recurso do QnA Maker. 
* **Publicar** página Definições. Se não tiver uma base de dados de conhecimento publicada, criar uma base de dados de conhecimento vazia, em seguida, importar uma base de dados de conhecimento sobre o **definições** página, em seguida, publique. Pode transferir e utilizar [esta base de dados de conhecimento básico](https://github.com/Azure-Samples/cognitive-services-sample-data-files/blob/master/qna-maker/knowledge-bases/basic-kb.tsv). 

    As definições de página de publicação incluem o valor de rotas de publicação, o valor do anfitrião e o valor de EndpointKey. 

    ![Definições de publicação](../media/qnamaker-quickstart-get-answer/publish-settings.png)

O código para este início rápido está no [ https://github.com/Azure-Samples/cognitive-services-qnamaker-nodejs ](https://github.com/Azure-Samples/cognitive-services-qnamaker-nodejs/tree/master/documentation-samples/quickstarts/get-answer) repositório. 

## <a name="create-a-nodejs-file"></a>Crie um ficheiro de node. js

Abra o VSCode e crie um novo ficheiro designado `get-answer.js`. 

## <a name="add-the-required-dependencies"></a>Adicionar as dependências necessárias

Na parte superior do `get-answer.js` de ficheiros, adicione as dependências necessárias ao projeto:

[!code-nodejs[Add the required dependencies](~/samples-qnamaker-nodejs/documentation-samples/quickstarts/get-answer/get-answer.js?range=1-4 "Add the required dependencies")]

## <a name="add-the-required-constants"></a>Adicionar as constantes necessárias

Em seguida, adicione as constantes necessárias para acessar o QnA Maker. Estes valores são sobre o **publicar** página depois de publicar a base de dados de conhecimento. 

[!code-nodejs[Add the required constants](~/samples-qnamaker-nodejs/documentation-samples/quickstarts/get-answer/get-answer.js?range=6-22 "Add the required constants")]

## <a name="add-a-post-request-to-send-question-and-get-an-answer"></a>Adicionar um pedido POST para enviar a pergunta e obter uma resposta

O código a seguir faz um pedido HTTPS para a API do QnA Maker para enviar a pergunta para a base de dados de conhecimento e recebe a resposta:

[!code-nodejs[Add a POST request to send question to knowledge base](~/samples-qnamaker-nodejs/documentation-samples/quickstarts/get-answer/get-answer.js?range=24-49 "Add a POST request to send question to knowledge base")]

O `Authorization` valor do cabeçalho inclui a cadeia de caracteres `EndpointKey `. 

## <a name="install-the-dependencies"></a>Instalar as dependências

Instale as dependências da linha de comando:

```bash
npm install request request-promise
```

## <a name="run-the-program"></a>Execute o programa

Execute o programa a partir da linha de comando. Ele enviará automaticamente o pedido para a API QnA Maker, em seguida, será impresso para a janela da consola.

Execute o ficheiro:

```bash
node get-answer.js
```

[!INCLUDE [JSON request and response](../../../../includes/cognitive-services-qnamaker-quickstart-get-answer-json.md)]

[!INCLUDE [Clean up files and knowledge base](../../../../includes/cognitive-services-qnamaker-quickstart-cleanup-resources.md)] 

## <a name="next-steps"></a>Passos Seguintes

> [!div class="nextstepaction"]
> [Referência à API REST do Criador de FAQ](https://westus.dev.cognitive.microsoft.com/docs/services/5a93fcf85b4ccd136866eb37/operations/5ac266295b4ccd1554da75ff)
