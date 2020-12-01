---
title: 'Início Rápido: criar base de dados de conhecimento - REST, Go - Criador de FAQ'
description: Este início rápido baseado em REST do Go descreve a criação programática de uma base de dados de conhecimento do Criador de FAQ de exemplo, que será apresentada no Dashboard do Azure da sua conta da API dos Serviços Cognitivos.
ms.service: cognitive-services
ms.subservice: qna-maker
ms.date: 12/16/2019
ROBOTS: NOINDEX,NOFOLLOW
ms.custom: RESTCURL2020FEB27
ms.topic: how-to
ms.openlocfilehash: cff2d141e8108d9a3e2e12764174a0bf4978182f
ms.sourcegitcommit: 9eda79ea41c60d58a4ceab63d424d6866b38b82d
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 11/30/2020
ms.locfileid: "96352325"
---
# <a name="quickstart-create-a-knowledge-base-in-qna-maker-using-go"></a>Início Rápido: criar uma base de dados de conhecimento no Criador de FAQ com o Go

Este início rápido descreve a criação, através de programação, de uma base de dados de conhecimento do Criador de FAQ. O Criador de FAQ extrai automaticamente perguntas e respostas de conteúdos semiestruturados, como FAQs, a partir de [origens de dados](../index.yml). O modelo da base de dados de conhecimento é definido no JSON enviado no corpo do pedido da API.

Este início rápido chama as API do Criador de FAQ:
* [Criar KB](/rest/api/cognitiveservices/qnamaker/knowledgebase/create)
* [Obter Detalhes da operação](/rest/api/cognitiveservices/qnamaker/operations/getdetails)

[Documentação de referência](/rest/api/cognitiveservices/qnamaker/knowledgebase)  |  [Amostra go](https://github.com/Azure-Samples/cognitive-services-qnamaker-go/blob/master/documentation-samples/quickstarts/create-knowledge-base/create-new-knowledge-base.go)

[!INCLUDE [Custom subdomains notice](../../../../includes/cognitive-services-custom-subdomains-note.md)]

## <a name="prerequisites"></a>Pré-requisitos

* [Go 1.10.1](https://golang.org/dl/)
* Tem de ter um [serviço Criador de FAQ](../How-To/set-up-qnamaker-service-azure.md). Para recuperar a sua chave e ponto final (que inclui o nome do recurso), selecione **Quickstart** para o seu recurso no portal Azure.

## <a name="create-a-knowledge-base-go-file"></a>Criar um ficheiro do Go de base de dados de conhecimento

Crie um ficheiro com o nome `create-new-knowledge-base.go`.

## <a name="add-the-required-dependencies"></a>Adicionar as dependências necessárias

Na parte superior do `create-new-knowledge-base.go`, adicione as seguintes linhas para adicionar as dependências necessárias ao projeto:

:::code language="go" source="~/cognitive-services-quickstart-code/go/QnAMaker/rest/create-kb.go" id="dependencies":::

## <a name="add-the-kb-model-definition"></a>Adicionar a definição de modelo de KB
Depois das constantes, adicione a seguinte definição de modelo de KB. Este modelo converte-se numa cadeia de carateres após a definição.

:::code language="go" source="~/cognitive-services-quickstart-code/go/QnAMaker/rest/create-kb.go" id="model":::

## <a name="add-supporting-structures-and-functions"></a>Adicionar estruturas e funções de suporte

Em seguida, adicione as funções de suporte seguintes.

1. Adicione a estrutura para uma resposta HTTP:

    :::code language="go" source="~/cognitive-services-quickstart-code/go/QnAMaker/rest/create-kb.go" id="response":::

1. Adicione o seguinte método para suportar um POST às APIs do Criador de FAQ. Neste início rápido, o POST é utilizado para enviar a definição de KB para o Criador de FAQ.

    :::code language="go" source="~/cognitive-services-quickstart-code/go/QnAMaker/rest/create-kb.go" id="post":::

1. Adicione o seguinte método para suportar um GET às APIs do Criador de FAQ. Neste início rápido, o GET serve para verificar o estado da operação de criação.

    :::code language="go" source="~/cognitive-services-quickstart-code/go/QnAMaker/rest/create-kb.go" id="get":::

## <a name="add-function-to-create-kb"></a>Adicionar função para criar KB

Adicione as seguintes funções para fazer um pedido POST de HTTP para criar a base de dados de conhecimento. É devolvido _create_ **ID da Operação** no campo do cabeçalho de resposta POST **Localização** e, em seguida, é utilizado como parte da rota no pedido GET. A `Ocp-Apim-Subscription-Key` é a chave de serviço do Criador de FAQ utilizada para autenticação.

:::code language="go" source="~/cognitive-services-quickstart-code/go/QnAMaker/rest/create-kb.go" id="create_kb":::

Esta chamada à API devolve uma resposta JSON, que inclui o ID da operação. Utilize o ID da operação para determinar se a KB for criada com êxito.

```JSON
{
  "operationState": "NotStarted",
  "createdTimestamp": "2018-09-26T05:19:01Z",
  "lastActionTimestamp": "2018-09-26T05:19:01Z",
  "userId": "XXX9549466094e1cb4fd063b646e1ad6",
  "operationId": "8dfb6a82-ae58-4bcb-95b7-d1239ae25681"
}
```

## <a name="add-function-to-get-status"></a>Adicionar a função para obter o estado

Adicione a função seguinte para fazer um pedido GET de HTTP para verificar o estado da operação. A `Ocp-Apim-Subscription-Key` é a chave de serviço do Criador de FAQ utilizada para autenticação.

:::code language="go" source="~/cognitive-services-quickstart-code/go/QnAMaker/rest/create-kb.go" id="get_status":::

Repita a chamada até ter êxito ou falhar:

```JSON
{
  "operationState": "Succeeded",
  "createdTimestamp": "2018-09-26T05:22:53Z",
  "lastActionTimestamp": "2018-09-26T05:23:08Z",
  "resourceLocation": "/knowledgebases/XXX7892b-10cf-47e2-a3ae-e40683adb714",
  "userId": "XXX9549466094e1cb4fd063b646e1ad6",
  "operationId": "177e12ff-5d04-4b73-b594-8575f9787963"
}
```
## <a name="add-main-function"></a>Adicionar função principal

A função seguinte é a função principal, que cria a KB e repete as verificações de estado. Uma vez que a criação da BDC pode demorar algum tempo, tem de repetir as chamadas para verificar o estado até que este seja com êxito ou falhe.

:::code language="go" source="~/cognitive-services-quickstart-code/go/QnAMaker/rest/create-kb.go" id="main":::


## <a name="compile-the-program"></a>Compilar o programa
Introduza o seguinte comando para compilar o ficheiro. A linha de comandos não devolve informações para uma compilação bem-sucedida.

```bash
go build create-new-knowledge-base.go
```

## <a name="run-the-program"></a>Execute o programa

Introduza o seguinte comando numa linha de comandos para executar o programa. Enviará o pedido à API do Criador de FAQ para criar a KB e, em seguida, irá consultar para obter os resultados a cada 30 segundos. Cada resposta é impressa na janela da consola.

```bash
go run create-new-knowledge-base
```

Assim que a sua base de dados de conhecimento é criada, pode visualizá-la no seu Portal do Criador de FAQ, na página [My knowledge bases](https://www.qnamaker.ai/Home/MyServices) (As minhas bases de dados de conhecimento).

[!INCLUDE [Clean up files and KB](../../../../includes/cognitive-services-qnamaker-quickstart-cleanup-resources.md)]

## <a name="next-steps"></a>Passos seguintes

> [!div class="nextstepaction"]
> [Referência à API REST do Criador de FAQ](/rest/api/cognitiveservices/qnamaker4.0/knowledgebase)