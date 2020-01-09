---
title: 'Início rápido: publicar a base de dados de conhecimento, REST, Python-QnA Maker'
titleSuffix: Azure Cognitive Services
description: Este guia de início rápido baseado em REST do Python publica sua base de dados de conhecimento e cria um ponto de extremidade que pode ser chamado em seu aplicativo ou bot de bate-papo.
services: cognitive-services
author: diberry
manager: nitinme
ms.custom: seodec18
ms.service: cognitive-services
ms.subservice: qna-maker
ms.topic: quickstart
ms.date: 12/16/2019
ms.author: diberry
ms.openlocfilehash: b93ba53996168d55e45e995a3fc11fdc3b889f7b
ms.sourcegitcommit: f4f626d6e92174086c530ed9bf3ccbe058639081
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 12/25/2019
ms.locfileid: "75447408"
---
# <a name="quickstart-publish-a-knowledge-base-in-qna-maker-using-python"></a>Início Rápido: publicar uma base de dados de conhecimento no Criador de FAQ com o Python

Este guia de introdução baseada em REST orienta-o através de publicação por meio de programação de sua base de dados de conhecimento (KB). A publicação envia por push a versão mais recente da base de dados de conhecimento para um índice de Pesquisa Cognitiva dedicado do Azure e cria um ponto de extremidade que pode ser chamado em seu aplicativo ou bot de chat.

Este início rápido chama QnA Maker APIs REST:
* [Publicar](https://docs.microsoft.com/rest/api/cognitiveservices/qnamaker/knowledgebase/publish) - esta API não requer quaisquer informações no corpo do pedido.

## <a name="prerequisites"></a>Pré-requisitos

* [Python 3.7](https://www.python.org/downloads/)
* Tem de ter um [serviço Criador de FAQ](../How-To/set-up-qnamaker-service-azure.md). Para recuperar a chave e o ponto de extremidade (que inclui o nome do recurso), selecione **início rápido** para seu recurso no portal do Azure.
* QnA Maker ID da base de dados de conhecimento (KB) encontrada na URL no parâmetro `kbid` cadeia de caracteres de consulta, conforme mostrado abaixo.

    ![ID da base de dados de conhecimento do Criador de FAQ](../media/qnamaker-quickstart-kb/qna-maker-id.png)

    Se ainda não tiver uma base de dados de conhecimento, pode criar uma de exemplo para utilizar neste guia de introdução: [Criar uma nova base de dados de conhecimento](../how-to/create-knowledge-base.md).

> [!NOTE]
> Os arquivos de solução completos estão disponíveis no [repositório **Azure-Samples/cognitiva-Services-qnamaker-Python** GitHub](https://github.com/Azure-Samples/cognitive-services-qnamaker-python/tree/master/documentation-samples/quickstarts/publish-knowledge-base).

## <a name="create-a-knowledge-base-python-file"></a>Criar um ficheiro do Python de base de dados de conhecimento

Crie um ficheiro com o nome `publish-kb-3x.py`.

## <a name="add-the-required-dependencies"></a>Adicionar as dependências necessárias

Na parte superior de `publish-kb-3x.py`, adicione as linhas seguintes para adicionar as dependências necessárias ao projeto:

[!code-python[Add the required dependencies](~/samples-qnamaker-python/documentation-samples/quickstarts/publish-knowledge-base/publish-kb-3x.py?range=1-1 "Add the required dependencies")]

## <a name="add-required-constants"></a>Adicionar constantes necessárias

Depois das dependências necessárias anteriores, adicione as constantes necessárias para aceder ao Criador de FAQ. Substitua os valores pelos seus próprios.

[!code-python[Add the required constants](~/samples-qnamaker-python/documentation-samples/quickstarts/publish-knowledge-base/publish-kb-3x.py?range=5-15 "Add the required constants")]

## <a name="add-post-request-to-publish-knowledge-base"></a>Adicionar pedido POST para publicar a base de dados de conhecimento

Depois das constantes necessárias, adicione o seguinte código, o que faz um pedido HTTPS para a API do QnA Maker para publicar uma base de dados de conhecimento e recebe a resposta:

[!code-python[Add a POST request to publish knowledge base](~/samples-qnamaker-python/documentation-samples/quickstarts/publish-knowledge-base/publish-kb-3x.py?range=17-26 "Add a POST request to publish knowledge base")]

A chamada à API devolve um estado 204 para uma publicação com êxito, sem qualquer conteúdo no corpo da resposta. O código adiciona conteúdo para respostas 204.

Para qualquer outra resposta, a resposta é devolvida inalterada.

## <a name="build-and-run-the-program"></a>Compilar e executar o programa

Introduza o comando seguinte numa linha de comandos para executar o programa. Irá enviar o pedido para a API do QnA Maker para publicar a base de dados de conhecimento, em seguida, imprimir 204 para êxito ou erros.

```bash
python publish-kb-3x.py
```

[!INCLUDE [Clean up files and knowledge base](../../../../includes/cognitive-services-qnamaker-quickstart-cleanup-resources.md)]

## <a name="next-steps"></a>Passos seguintes

Depois da base de dados de conhecimento é publicado, é necessário o [URL de ponto final para gerar uma resposta](../Tutorials/create-publish-answer.md#generating-an-answer).

> [!div class="nextstepaction"]
> [Referência à API REST do Criador de FAQ](https://go.microsoft.com/fwlink/?linkid=2092179)

[Descrição geral do Criador de FAQ](../Overview/overview.md)
