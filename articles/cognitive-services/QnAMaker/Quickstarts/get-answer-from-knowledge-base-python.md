---
title: 'Início rápido: Obter resposta da base de dados de conhecimento - REST, o Python - QnA Maker'
titlesuffix: Azure Cognitive Services
description: Este guia de introdução baseada em REST de Python explica como obter uma resposta de uma base de dados de conhecimento, por meio de programação.
services: cognitive-services
author: diberry
manager: nitinme
ms.service: cognitive-services
ms.subservice: qna-maker
ms.topic: quickstart
ms.date: 02/28/2019
ms.author: diberry
ms.openlocfilehash: bd25235f10f25490edfe9f6dc4ea8ab5037cdc8a
ms.sourcegitcommit: c712cb5c80bed4b5801be214788770b66bf7a009
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/01/2019
ms.locfileid: "57212995"
---
# <a name="get-answers-to-a-question-from-a-knowledge-base-with-python"></a>Obtenha respostas a uma pergunta de uma base de dados de conhecimento com Python

Neste início rápido explica como obter programaticamente uma resposta de uma base de dados de conhecimento publicada do QnA Maker. A base de dados de conhecimento contém perguntas e respostas partir [origens de dados](../Concepts/data-sources-supported.md) como FAQs. O [pergunta](../how-to/metadata-generateanswer-usage.md#generateanswer-request-configuration) é enviado para o serviço QnA Maker. O [resposta](../how-to/metadata-generateanswer-usage.md#generateanswer-response-properties) inclui a resposta previstos para parte superior. 

## <a name="prerequisites"></a>Pré-requisitos

* [Python 3.6 ou superior](https://www.python.org/downloads/)
* [Visual Studio Code](https://code.visualstudio.com/)
* Tem de ter um [serviço Criador de FAQ](../How-To/set-up-qnamaker-service-azure.md). Para obter a sua chave, selecione **chaves** sob **gestão de recursos** no dashboard do Azure para o seu recurso do QnA Maker. 
* **Publicar** página Definições. Se não tiver uma base de dados de conhecimento publicada, criar uma base de dados de conhecimento vazia, em seguida, importar uma base de dados de conhecimento sobre o **definições** página, em seguida, publique. Pode transferir e utilizar [esta base de dados de conhecimento básico](https://github.com/Azure-Samples/cognitive-services-sample-data-files/blob/master/qna-maker/knowledge-bases/basic-kb.tsv). 

    As definições de página de publicação incluem o valor de rotas de publicação, o valor do anfitrião e o valor de EndpointKey. 

    ![Definições de publicação](../media/qnamaker-quickstart-get-answer/publish-settings.png)

O código para este início rápido está no [ https://github.com/Azure-Samples/cognitive-services-qnamaker-python ](https://github.com/Azure-Samples/cognitive-services-qnamaker-python/tree/master/documentation-samples/quickstarts/get-answer) repositório. 

## <a name="create-a-python-file"></a>Crie um ficheiro de Python

Abra o VSCode e crie um novo ficheiro designado `get-answer-3x.py`.

## <a name="add-the-required-dependencies"></a>Adicionar as dependências necessárias

Na parte superior do `get-answer-3x.py` de ficheiros, adicione as dependências necessárias ao projeto:

[!code-python[Add the required dependencies](~/samples-qnamaker-python/documentation-samples/quickstarts/get-answer/get-answer-3x.py?range=1-2 "Add the required dependencies")]

O anfitrião e a rota são diferentes da forma como surgem na **publicar** página. Isto acontece porque a biblioteca de python não permite a qualquer roteamento no anfitrião. O encaminhamento é exibido no **publicar** página como parte de anfitrião foi movido para a rota.

## <a name="add-the-required-constants"></a>Adicionar as constantes necessárias

Adicione as constantes necessárias para acessar o QnA Maker. Estes valores são sobre o **publicar** página depois de publicar a base de dados de conhecimento. 

[!code-python[Add the required constants](~/samples-qnamaker-python/documentation-samples/quickstarts/get-answer/get-answer-3x.py?range=5-25 "Add the required constants")]

## <a name="add-a-post-request-to-send-question-and-get-an-answer"></a>Adicionar um pedido POST para enviar a pergunta e obter uma resposta

O código a seguir faz um pedido HTTPS para a API do QnA Maker para enviar a pergunta para a base de dados de conhecimento e recebe a resposta:

[!code-python[Add a POST request to send question to knowledge base](~/samples-qnamaker-python/documentation-samples/quickstarts/get-answer/get-answer-3x.py?range=27-48 "Add a POST request to send question to knowledge base")]

O `Authorization` valor do cabeçalho inclui a cadeia de caracteres `EndpointKey `. 

## <a name="run-the-program"></a>Execute o programa

Execute o programa a partir da linha de comando. Ele enviará automaticamente o pedido para a API QnA Maker, em seguida, será impresso para a janela da consola.

Execute o ficheiro:

```bash
python get-answer-3x.py
```

[!INCLUDE [JSON request and response](../../../../includes/cognitive-services-qnamaker-quickstart-get-answer-json.md)] 


[!INCLUDE [Clean up files and knowledge base](../../../../includes/cognitive-services-qnamaker-quickstart-cleanup-resources.md)] 

## <a name="next-steps"></a>Passos Seguintes

> [!div class="nextstepaction"]
> [Referência à API REST do Criador de FAQ](https://westus.dev.cognitive.microsoft.com/docs/services/5a93fcf85b4ccd136866eb37/operations/5ac266295b4ccd1554da75ff)
