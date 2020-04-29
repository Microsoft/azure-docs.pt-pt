---
title: 'Quickstart: Publicar base de conhecimento, REST, Python - QnA Maker'
description: Este quickstart baseado em Python REST publica a sua base de conhecimento e cria um ponto final que pode ser chamado na sua aplicação ou chat bot.
ms.date: 02/08/2020
ROBOTS: NOINDEX,NOFOLLOW
ms.custom: RESTCURL2020FEB27
ms.topic: conceptual
ms.openlocfilehash: 6f053ecbd58d3c2527c1b904437dcc4715c76af1
ms.sourcegitcommit: 58faa9fcbd62f3ac37ff0a65ab9357a01051a64f
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/29/2020
ms.locfileid: "78851654"
---
# <a name="quickstart-publish-a-knowledge-base-in-qna-maker-using-python"></a>Início Rápido: publicar uma base de dados de conhecimento no Criador de FAQ com o Python

Este quickstart baseado em REST acompanha-o através da publicação programática da sua base de conhecimentos (KB). A publicação empurra a versão mais recente da base de conhecimento para um índice dedicado de Pesquisa Cognitiva Azure e cria um ponto final que pode ser chamado na sua aplicação ou chat bot.

Este quickstart chama QnA Maker REST APIs:
* [Publicar](https://docs.microsoft.com/rest/api/cognitiveservices/qnamaker/knowledgebase/publish) - esta API não requer quaisquer informações no corpo do pedido.

## <a name="prerequisites"></a>Pré-requisitos

* [Python 3.7](https://www.python.org/downloads/)
* Tem de ter um [serviço Criador de FAQ](../How-To/set-up-qnamaker-service-azure.md). Para recuperar a sua chave e ponto final (que inclui o nome do recurso), selecione **Quickstart** para o seu recurso no portal Azure.
* QnA Maker base de conhecimento (KB) `kbid` ID encontrado no URL no parâmetro de corda de consulta, como mostrado abaixo.

    ![ID da base de dados de conhecimento do Criador de FAQ](../media/qnamaker-quickstart-kb/qna-maker-id.png)

    Se ainda não tiver uma base de dados de conhecimento, pode criar uma de exemplo para utilizar neste guia de introdução: [Criar uma nova base de dados de conhecimento](../how-to/create-knowledge-base.md).

> [!NOTE]
> Os ficheiros completos de solução estão disponíveis no [repositório **Azure-Samples/cognitive-services-qnamaker-python** GitHub](https://github.com/Azure-Samples/cognitive-services-qnamaker-python/tree/master/documentation-samples/quickstarts/publish-knowledge-base).

## <a name="create-a-knowledge-base-python-file"></a>Criar um ficheiro do Python de base de dados de conhecimento

Crie um ficheiro com o nome `publish-kb-3x.py`.

## <a name="add-the-required-dependencies"></a>Adicionar as dependências necessárias

Na parte superior do `publish-kb-3x.py`, adicione as seguintes linhas para adicionar as dependências necessárias ao projeto:

[!code-python[Add the required dependencies](~/samples-qnamaker-python/documentation-samples/quickstarts/publish-knowledge-base/publish-kb-3x.py?range=1-1 "Add the required dependencies")]

## <a name="add-required-constants"></a>Adicionar constantes necessárias

Depois das dependências necessárias anteriores, adicione as constantes necessárias para aceder ao Criador de FAQ. Substitua os valores por si próprio.

[!code-python[Add the required constants](~/samples-qnamaker-python/documentation-samples/quickstarts/publish-knowledge-base/publish-kb-3x.py?range=5-15 "Add the required constants")]

## <a name="add-post-request-to-publish-knowledge-base"></a>Adicionar pedido post para publicar base de conhecimento

Após as constantes necessárias, adicione o seguinte código, que faz um pedido HTTPS à API do Fabricante de QnA para publicar uma base de conhecimento e recebe a resposta:

[!code-python[Add a POST request to publish knowledge base](~/samples-qnamaker-python/documentation-samples/quickstarts/publish-knowledge-base/publish-kb-3x.py?range=17-26 "Add a POST request to publish knowledge base")]

A chamada à API devolve um estado 204 para uma publicação com êxito, sem qualquer conteúdo no corpo da resposta. O código adiciona conteúdo para respostas 204.

Para qualquer outra resposta, a resposta é devolvida inalterada.

## <a name="build-and-run-the-program"></a>Compilar e executar o programa

Introduza o seguinte comando numa linha de comandos para executar o programa. Enviará o pedido à API da QnA Maker para publicar a base de conhecimentos e, em seguida, imprimir204 para sucesso ou erros.

```bash
python publish-kb-3x.py
```

[!INCLUDE [Clean up files and knowledge base](../../../../includes/cognitive-services-qnamaker-quickstart-cleanup-resources.md)]

## <a name="next-steps"></a>Passos seguintes

Após a publicação da base de conhecimentos, é necessário que o [URL do ponto final gere uma resposta](./get-answer-from-knowledge-base-python.md).

> [!div class="nextstepaction"]
> [Referência à API REST do Criador de FAQ](https://go.microsoft.com/fwlink/?linkid=2092179)

[Descrição geral do Criador de FAQ](../Overview/overview.md)