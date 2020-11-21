---
title: 'Quickstart: Obtenha resposta da base de conhecimento - REST, Python - QnA Maker'
description: Este quickstart baseado em Python REST acompanha-o através de obter uma resposta de uma base de conhecimento, programáticamente.
ms.service: cognitive-services
ms.subservice: qna-maker
ms.date: 02/08/2020
ROBOTS: NOINDEX,NOFOLLOW
ms.custom: RESTCURL2020FEB27, devx-track-python
ms.topic: how-to
ms.openlocfilehash: 29e6f3634ab723cbcba948d6f8c35ac66732c2f9
ms.sourcegitcommit: 10d00006fec1f4b69289ce18fdd0452c3458eca5
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 11/21/2020
ms.locfileid: "95023655"
---
# <a name="quickstart-get-answers-to-a-question-from-a-knowledge-base-with-python"></a>Quickstart: Obtenha respostas para uma pergunta de uma base de conhecimento com Python

Este quickstart acompanha-o programáticamente recebendo uma resposta de uma base de conhecimento da QnA Maker publicada. A base de conhecimento contém perguntas e respostas de fontes de [dados,](../Concepts/knowledge-base.md) tais como PERGUNTAS Frequentes. A [questão](../how-to/metadata-generateanswer-usage.md#generateanswer-request-configuration) é enviada para o serviço QnA Maker. A [resposta](../how-to/metadata-generateanswer-usage.md#generateanswer-response-properties) inclui a resposta mais prevista.

[Documentação de referência](https://docs.microsoft.com/rest/api/cognitiveservices/qnamaker4.0/Runtime)  |  [Amostra](https://github.com/Azure-Samples/cognitive-services-qnamaker-python/blob/master/documentation-samples/quickstarts/get-answer/get-answer-3x.py)

## <a name="prerequisites"></a>Pré-requisitos

* [Python 3.6 ou maior](https://www.python.org/downloads/)
* [Visual Studio Code](https://code.visualstudio.com/)
* Tem de ter um [serviço Criador de FAQ](../How-To/set-up-qnamaker-service-azure.md). Para recuperar a sua chave, selecione **Keys** sob **Gestão de Recursos** no seu painel Azure para o seu recurso QnA Maker.
* **Publicar** definições de página. Se não tiver uma base de conhecimento publicada, crie uma base de conhecimento vazia e, em seguida, importe uma base de conhecimento na página **Definições** e, em seguida, publique. Você pode baixar e usar [esta base de conhecimento básico.](https://github.com/Azure-Samples/cognitive-services-sample-data-files/blob/master/qna-maker/knowledge-bases/basic-kb.tsv)

    As definições da página de publicação incluem o valor da rota POST, o valor do anfitrião e o valor endpointKey.

    ![Definições de publicação](../media/qnamaker-quickstart-get-answer/publish-settings.png)

## <a name="create-a-python-file"></a>Criar um ficheiro Python

Abra o VSCode e crie um novo ficheiro chamado `get-answer-3x.py` .

## <a name="add-the-required-dependencies"></a>Adicionar as dependências necessárias

No topo do ficheiro, adicione as `get-answer-3x.py` dependências necessárias ao projeto:

:::code language="python" source="~/cognitive-services-quickstart-code/python/QnAMaker/rest/query-kb.py" id="dependencies":::

<!--TBD - reword this following paragraph -->

O anfitrião e a rota são diferentes do que aparecem na página **publicar.** Isto porque a biblioteca python não permite qualquer encaminhamento no hospedeiro. O encaminhamento que aparece na página **publicar** como parte do anfitrião foi movido para a rota.

## <a name="add-the-required-constants"></a>Adicionar as constantes necessárias

Adicione as constantes necessárias para aceder ao QnA Maker. Estes valores estão na página **publicar** depois de publicar a base de conhecimentos.

:::code language="python" source="~/cognitive-services-quickstart-code/python/QnAMaker/rest/query-kb.py" id="constants":::

## <a name="add-a-post-request-to-send-question-and-get-an-answer"></a>Adicione um pedido de CORREIO para enviar a pergunta e obter uma resposta

O seguinte código faz um pedido HTTPS à API do Fabricante QNA para enviar a pergunta para a base de conhecimento e recebe a resposta:

:::code language="python" source="~/cognitive-services-quickstart-code/python/QnAMaker/rest/query-kb.py" id="main":::

O `Authorization` valor do cabeçalho inclui a `EndpointKey` corda.

## <a name="run-the-program"></a>Execute o programa

Execute o programa a partir da linha de comandos. Enviará automaticamente o pedido para a API do Criador QnA, e depois imprimirá na janela da consola.

Executar o ficheiro:

```bash
python get-answer-3x.py
```

[!INCLUDE [JSON request and response](../../../../includes/cognitive-services-qnamaker-quickstart-get-answer-json.md)]

Saiba mais sobre o [pedido](../how-to/metadata-generateanswer-usage.md#generateanswer-request) e [resposta.](../how-to/metadata-generateanswer-usage.md#generateanswer-response)

[!INCLUDE [Clean up files and knowledge base](../../../../includes/cognitive-services-qnamaker-quickstart-cleanup-resources.md)]

## <a name="next-steps"></a>Próximos passos

> [!div class="nextstepaction"]
> [Referência à API REST do Criador de FAQ](https://go.microsoft.com/fwlink/?linkid=2092179)
