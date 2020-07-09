---
title: 'Quickstart: Obtenha resposta da base de conhecimento - REST, Node.js - QnA Maker'
description: Este quickstart baseado em Node.js REST acompanha-o através de obter uma resposta de uma base de conhecimento, programáticamente.
ms.date: 02/08/2020
ROBOTS: NOINDEX,NOFOLLOW
ms.custom: RESTCURL2020FEB27
ms.topic: how-to
ms.openlocfilehash: 3003f37789634c13c2213b708f7ae963c97da873
ms.sourcegitcommit: 61d850bc7f01c6fafee85bda726d89ab2ee733ce
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 06/03/2020
ms.locfileid: "84342787"
---
# <a name="quickstart-get-answers-to-a-question-from-a-knowledge-base-with-nodejs"></a>Quickstart: Obtenha respostas para uma pergunta de uma base de conhecimento com Node.js

Este quickstart acompanha-o programáticamente recebendo uma resposta de uma base de conhecimento da QnA Maker publicada. A base de conhecimento contém perguntas e respostas de fontes de [dados,](../Concepts/knowledge-base.md) tais como PERGUNTAS Frequentes. A [questão](../how-to/metadata-generateanswer-usage.md#generateanswer-request-configuration) é enviada para o serviço QnA Maker. A [resposta](../how-to/metadata-generateanswer-usage.md#generateanswer-response-properties) inclui a resposta mais prevista.

[Documentação de referência](https://docs.microsoft.com/rest/api/cognitiveservices/qnamakerruntime/runtime)  |  [Amostra](https://github.com/Azure-Samples/cognitive-services-qnamaker-nodejs/blob/master/documentation-samples/quickstarts/get-answer/get-answer.js)

## <a name="prerequisites"></a>Pré-requisitos

* [Node.js](https://nodejs.org/en/download/)
* [Visual Studio Code](https://code.visualstudio.com/)
* Tem de ter um [serviço Criador de FAQ](../How-To/set-up-qnamaker-service-azure.md). Para recuperar a sua chave, selecione **Keys** sob **Gestão de Recursos** no seu painel Azure para o seu recurso QnA Maker.
* **Publicar** definições de página. Se não tiver uma base de conhecimento publicada, crie uma base de conhecimento vazia e, em seguida, importe uma base de conhecimento na página **Definições** e, em seguida, publique. Você pode baixar e usar [esta base de conhecimento básico.](https://github.com/Azure-Samples/cognitive-services-sample-data-files/blob/master/qna-maker/knowledge-bases/basic-kb.tsv)

    As definições da página de publicação incluem o valor da rota POST, o valor do anfitrião e o valor endpointKey.

    ![Publish settings (Definições de publicação)](../media/qnamaker-quickstart-get-answer/publish-settings.png)

## <a name="create-a-nodejs-file"></a>Criar um ficheiro Node.js

Abra o VSCode e crie um novo ficheiro chamado `get-answer.js` .

## <a name="add-the-required-dependencies"></a>Adicionar as dependências necessárias

No topo do ficheiro, adicione as `get-answer.js` dependências necessárias ao projeto:

[!code-nodejs[Add the required dependencies](~/samples-qnamaker-nodejs/documentation-samples/quickstarts/get-answer/get-answer.js?range=1-4 "Add the required dependencies")]

## <a name="add-the-required-constants"></a>Adicionar as constantes necessárias

Em seguida, adicione as constantes necessárias para aceder ao QnA Maker. Estes valores estão na página **publicar** depois de publicar a base de conhecimentos.

[!code-nodejs[Add the required constants](~/samples-qnamaker-nodejs/documentation-samples/quickstarts/get-answer/get-answer.js?range=6-22 "Add the required constants")]

## <a name="add-a-post-request-to-send-question-and-get-an-answer"></a>Adicione um pedido de CORREIO para enviar a pergunta e obter uma resposta

O seguinte código faz um pedido HTTPS à API do Fabricante QNA para enviar a pergunta para a base de conhecimento e recebe a resposta:

[!code-nodejs[Add a POST request to send question to knowledge base](~/samples-qnamaker-nodejs/documentation-samples/quickstarts/get-answer/get-answer.js?range=24-49 "Add a POST request to send question to knowledge base")]

O `Authorization` valor do cabeçalho inclui a `EndpointKey` corda.

## <a name="install-the-dependencies"></a>Instalar as dependências

Instalar as dependências da linha de comando:

```bash
npm install request request-promise
```

## <a name="run-the-program"></a>Execute o programa

Executar o programa a partir da linha de comando. Enviará automaticamente o pedido para a API do Criador QnA, e depois imprimirá na janela da consola.

Executar o ficheiro:

```bash
node get-answer.js
```

[!INCLUDE [JSON request and response](../../../../includes/cognitive-services-qnamaker-quickstart-get-answer-json.md)]

Saiba mais sobre o [pedido](../how-to/metadata-generateanswer-usage.md#generateanswer-request) e [resposta.](../how-to/metadata-generateanswer-usage.md#generateanswer-response)

[!INCLUDE [Clean up files and knowledge base](../../../../includes/cognitive-services-qnamaker-quickstart-cleanup-resources.md)]

## <a name="next-steps"></a>Próximos passos

> [!div class="nextstepaction"]
> [Referência à API REST do Criador de FAQ](https://go.microsoft.com/fwlink/?linkid=2092179)