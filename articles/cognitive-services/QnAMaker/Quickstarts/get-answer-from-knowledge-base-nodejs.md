---
title: 'Quickstart: Obtenha resposta da base de conhecimento - REST, Node.js - QnA Maker'
description: Este quickstart baseado no Nó leva-o através de obter uma resposta de uma base de conhecimento, programáticamente.
ms.date: 02/08/2020
ROBOTS: NOINDEX,NOFOLLOW
ms.custom: RESTCURL2020FEB27
ms.topic: conceptual
ms.openlocfilehash: 9031c320b055c0a7191655d6531eaa31d9cc8bb8
ms.sourcegitcommit: f5e4d0466b417fa511b942fd3bd206aeae0055bc
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/06/2020
ms.locfileid: "78851731"
---
# <a name="quickstart-get-answers-to-a-question-from-a-knowledge-base-with-nodejs"></a>Quickstart: Obtenha respostas para uma pergunta de uma base de conhecimento com Node.js

Este quickstart leva-o programáticamente a obter uma resposta de uma base de conhecimento publicada da QnA Maker. A base de conhecimento contém perguntas e respostas de [fontes](../Concepts/knowledge-base.md) de dados como FAQs. A [questão](../how-to/metadata-generateanswer-usage.md#generateanswer-request-configuration) é enviada ao serviço QnA Maker. A [resposta](../how-to/metadata-generateanswer-usage.md#generateanswer-response-properties) inclui a resposta mais bem prevista.

[Documentação de referência](https://docs.microsoft.com/rest/api/cognitiveservices/qnamakerruntime/runtime) | [Amostra](https://github.com/Azure-Samples/cognitive-services-qnamaker-nodejs/blob/master/documentation-samples/quickstarts/get-answer/get-answer.js)

## <a name="prerequisites"></a>Pré-requisitos

* [Node.js](https://nodejs.org/en/download/)
* [Visual Studio Code](https://code.visualstudio.com/)
* Tem de ter um [serviço Criador de FAQ](../How-To/set-up-qnamaker-service-azure.md). Para recuperar a sua chave, selecione **Keys** sob **Gestão** de Recursos no seu painel Azure para o seu recurso QnA Maker.
* **Publique** as definições da página. Se não tiver uma base de conhecimento publicada, crie uma base de conhecimento vazia e, em seguida, importe uma base de conhecimento na página **Definições** e, em seguida, publique. Pode descarregar e utilizar [esta base de conhecimentos básicos.](https://github.com/Azure-Samples/cognitive-services-sample-data-files/blob/master/qna-maker/knowledge-bases/basic-kb.tsv)

    As definições da página de publicação incluem o valor da rota POST, o valor do anfitrião e o valor EndpointKey.

    ![Definições de publicação](../media/qnamaker-quickstart-get-answer/publish-settings.png)

## <a name="create-a-nodejs-file"></a>Crie um ficheiro Node.js

Abra o VSCode e crie um novo ficheiro chamado `get-answer.js`.

## <a name="add-the-required-dependencies"></a>Adicionar as dependências necessárias

No topo do ficheiro `get-answer.js`, adicione as dependências necessárias ao projeto:

[!code-nodejs[Add the required dependencies](~/samples-qnamaker-nodejs/documentation-samples/quickstarts/get-answer/get-answer.js?range=1-4 "Add the required dependencies")]

## <a name="add-the-required-constants"></a>Adicionar as constantes necessárias

Em seguida, adicione as constantes necessárias para aceder ao QnA Maker. Estes valores estão na página **Publicar** depois de publicar a base de conhecimento.

[!code-nodejs[Add the required constants](~/samples-qnamaker-nodejs/documentation-samples/quickstarts/get-answer/get-answer.js?range=6-22 "Add the required constants")]

## <a name="add-a-post-request-to-send-question-and-get-an-answer"></a>Adicione um pedido post para enviar a pergunta e obter uma resposta

O seguinte código faz um pedido HTTPS à API do Fabricante de QnA para enviar a questão para a base de conhecimento e recebe a resposta:

[!code-nodejs[Add a POST request to send question to knowledge base](~/samples-qnamaker-nodejs/documentation-samples/quickstarts/get-answer/get-answer.js?range=24-49 "Add a POST request to send question to knowledge base")]

O valor do cabeçalho `Authorization` inclui a cadeia `EndpointKey`.

## <a name="install-the-dependencies"></a>Instalar as dependências

Instale as dependências da linha de comando:

```bash
npm install request request-promise
```

## <a name="run-the-program"></a>Execute o programa

Executar o programa a partir da linha de comando. Ele enviará automaticamente o pedido para a API QnA Maker, em seguida, será impresso para a janela da consola.

Execute o ficheiro:

```bash
node get-answer.js
```

[!INCLUDE [JSON request and response](../../../../includes/cognitive-services-qnamaker-quickstart-get-answer-json.md)]

Saiba mais sobre o [pedido](../how-to/metadata-generateanswer-usage.md#generateanswer-request) e [resposta.](../how-to/metadata-generateanswer-usage.md#generateanswer-response)

[!INCLUDE [Clean up files and knowledge base](../../../../includes/cognitive-services-qnamaker-quickstart-cleanup-resources.md)]

## <a name="next-steps"></a>Passos seguintes

> [!div class="nextstepaction"]
> [Referência da API REST para o Criador de FAQ (V4)](https://go.microsoft.com/fwlink/?linkid=2092179)