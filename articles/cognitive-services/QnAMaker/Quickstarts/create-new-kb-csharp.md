---
title: 'Início Rápido: criar base de dados de conhecimento - REST, C# - Criador de FAQ'
description: Este início rápido baseado em REST do C# descreve a criação programática de uma base de dados de conhecimento do Criador de FAQ de exemplo, que será apresentada no Dashboard do Azure da sua conta da API dos Serviços Cognitivos.
ms.service: cognitive-services
ms.subservice: qna-maker
ms.date: 12/16/2019
ROBOTS: NOINDEX,NOFOLLOW
ms.custom: RESTCURL2020FEB27
ms.topic: how-to
ms.openlocfilehash: e31345c3b83e1ff5e01952d69dde9353b8234757
ms.sourcegitcommit: 9eda79ea41c60d58a4ceab63d424d6866b38b82d
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 11/30/2020
ms.locfileid: "96338117"
---
# <a name="quickstart-create-a-knowledge-base-in-qna-maker-using-c-with-rest"></a>Quickstart: Criar uma base de conhecimento no QnA Maker usando C# com REST

Este início rápido descreve a criação e publicação, através de programação, de uma base de dados de conhecimento do Criador de FAQ. O Criador de FAQ extrai automaticamente perguntas e respostas de conteúdos semiestruturados, como FAQs, a partir de [origens de dados](../index.yml). O modelo da base de dados de conhecimento é definido no JSON enviado no corpo do pedido da API.

Este início rápido chama as API do Criador de FAQ:
* [Criar KB](/rest/api/cognitiveservices/qnamaker/knowledgebase/create)
* [Obter Detalhes da operação](/rest/api/cognitiveservices/qnamaker/operations/getdetails)

[Documentação de referência](/rest/api/cognitiveservices/qnamaker/knowledgebase)  |  [Amostra C#](https://github.com/Azure-Samples/cognitive-services-qnamaker-csharp/blob/master/documentation-samples/quickstarts/create-knowledge-base/QnaQuickstartCreateKnowledgebase/Program.cs)

[!INCLUDE [Custom subdomains notice](../../../../includes/cognitive-services-custom-subdomains-note.md)]

## <a name="prerequisites"></a>Pré-requisitos

* A versão atual de [.NET Core](https://dotnet.microsoft.com/download/dotnet-core).
* Deve ter um [recurso QnA Maker.](../How-To/set-up-qnamaker-service-azure.md) Para recuperar a sua chave e ponto final (que inclui o nome do recurso), selecione **Quickstart** para o seu recurso no portal Azure.

### <a name="create-a-new-c-application"></a>Criar uma nova aplicação C#

Crie uma nova aplicação .NET Core no seu editor preferido ou IDE.

Numa janela de consola (como cmd, PowerShell ou Bash), utilize o `dotnet new` comando para criar uma nova aplicação de consola com o nome `qna-maker-quickstart` . Este comando cria um projeto simples "Hello World" C# com um único ficheiro de origem: *Program.cs*.

```dotnetcli
dotnet new console -n qna-maker-quickstart
```

Mude o seu diretório para a pasta de aplicações recém-criada. Pode construir a aplicação com:

```dotnetcli
dotnet build
```

A saída de construção não deve conter avisos ou erros.

```console
...
Build succeeded.
 0 Warning(s)
 0 Error(s)
...
```

## <a name="add-the-required-dependencies"></a>Adicionar as dependências necessárias

Na parte superior de Program.cs, substitua a instrução using única pelas seguintes linhas para adicionar as dependências necessárias ao projeto:

:::code language="csharp" source="~/cognitive-services-quickstart-code/dotnet/QnAMaker/rest/create-kb.cs" id="dependencies":::

## <a name="add-the-required-constants"></a>Adicionar as constantes necessárias

No topo da classe Program, adicione as constantes necessárias para aceder ao QnA Maker.

Definir os seguintes valores em variáveis ambientais:

* `QNA_MAKER_SUBSCRIPTION_KEY` - A **chave** é uma cadeia de 32 caracteres e está disponível no portal Azure, no recurso QnA Maker, na página Quickstart. Isto não é o mesmo que a chave final de previsão.
* `QNA_MAKER_ENDPOINT` - O **ponto final** é o URL para autoria, no formato de `https://YOUR-RESOURCE-NAME.cognitiveservices.azure.com` . Este não é o mesmo URL usado para consultar o ponto final de previsão.

:::code language="csharp" source="~/cognitive-services-quickstart-code/dotnet/QnAMaker/rest/create-kb.cs" id="constants":::

## <a name="add-the-kb-definition"></a>Adicionar a definição de KB

Depois das constantes, adicione a seguinte definição de KB:

:::code language="csharp" source="~/cognitive-services-quickstart-code/dotnet/QnAMaker/rest/create-kb.cs" id="kb":::

## <a name="add-supporting-functions-and-structures"></a>Adicionar funções e estruturas de suporte
Adicione o seguinte bloco de código dentro da classe Programa:

:::code language="csharp" source="~/cognitive-services-quickstart-code/dotnet/QnAMaker/rest/create-kb.cs" id="support":::

## <a name="add-a-post-request-to-create-kb"></a>Adicionar um pedido POST para criar KB

O código seguinte faz um pedido HTTPS à API do Criador de FAQ para criar uma KB e recebe a resposta:

:::code language="csharp" source="~/cognitive-services-quickstart-code/dotnet/QnAMaker/rest/create-kb.cs" id="post":::

:::code language="csharp" source="~/cognitive-services-quickstart-code/dotnet/QnAMaker/rest/create-kb.cs" id="post_create_kb":::

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

## <a name="add-get-request-to-determine-creation-status"></a>Adicionar pedido GET para determinar o estado de criação

Verifique o estado da operação.

:::code language="csharp" source="~/cognitive-services-quickstart-code/dotnet/QnAMaker/rest/create-kb.cs" id="get":::

:::code language="csharp" source="~/cognitive-services-quickstart-code/dotnet/QnAMaker/rest/create-kb.cs" id="get_status":::

Esta chamada à API devolve uma resposta JSON que inclui o estado da operação:

```JSON
{
  "operationState": "NotStarted",
  "createdTimestamp": "2018-09-26T05:22:53Z",
  "lastActionTimestamp": "2018-09-26T05:22:53Z",
  "userId": "XXX9549466094e1cb4fd063b646e1ad6",
  "operationId": "177e12ff-5d04-4b73-b594-8575f9787963"
}
```

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

## <a name="add-createkb-method"></a>Adicionar método CreateKB

O método seguinte cria a KB e repete as verificações de estado.  É devolvido _create_ **ID da Operação** no campo do cabeçalho de resposta POST **Localização** e, em seguida, é utilizado como parte da rota no pedido GET. Uma vez que a criação da BDC pode demorar algum tempo, tem de repetir as chamadas para verificar o estado até que este seja com êxito ou falhe. Quando a operação for concluída com êxito, o ID da BDC é devolvido em **resourceLocation**.

:::code language="csharp" source="~/cognitive-services-quickstart-code/dotnet/QnAMaker/rest/create-kb.cs" id="create_kb":::

## <a name="add-the-createkb-method-to-main"></a>Adicionar o método CreateKB ao Main

Altere o método Main para chamar o método CreateKB:

:::code language="csharp" source="~/cognitive-services-quickstart-code/dotnet/QnAMaker/rest/create-kb.cs" id="main":::

## <a name="build-and-run-the-program"></a>Compilar e executar o programa

Crie e execute o programa. Enviará automaticamente o pedido à API do Criador de FAQ para criar a KB e, em seguida, irá consultar para obter os resultados a cada 30 segundos. Cada resposta é impressa na janela da consola.

Assim que a sua base de dados de conhecimento é criada, pode visualizá-la no seu Portal do Criador de FAQ, na página [My knowledge bases](https://www.qnamaker.ai/Home/MyServices) (As minhas bases de dados de conhecimento).


[!INCLUDE [Clean up files and KB](../../../../includes/cognitive-services-qnamaker-quickstart-cleanup-resources.md)]

## <a name="next-steps"></a>Passos seguintes

> [!div class="nextstepaction"]
> [Referência à API REST do Criador de FAQ](/rest/api/cognitiveservices/qnamaker4.0/knowledgebase)