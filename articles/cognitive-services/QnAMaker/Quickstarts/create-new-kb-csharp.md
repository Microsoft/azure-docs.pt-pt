---
title: 'Início Rápido: criar base de dados de conhecimento - REST, C# - Criador de FAQ'
titlesuffix: Azure Cognitive Services
description: Este início rápido baseado em REST do C# descreve a criação programática de uma base de dados de conhecimento do Criador de FAQ de exemplo, que será apresentada no Dashboard do Azure da sua conta da API dos Serviços Cognitivos.
services: cognitive-services
author: diberry
manager: cgronlun
ms.service: cognitive-services
ms.component: qna-maker
ms.topic: quickstart
ms.date: 11/6/2018
ms.author: diberry
ms.openlocfilehash: 190a937a4239c08d844d271d2e1d8e858215a5c2
ms.sourcegitcommit: 922f7a8b75e9e15a17e904cc941bdfb0f32dc153
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 11/27/2018
ms.locfileid: "52334964"
---
# <a name="quickstart-create-a-knowledge-base-in-qna-maker-using-c"></a>Início Rápido: criar uma base de dados de conhecimento no Criador de FAQ com o C#

Este início rápido descreve a criação e publicação, através de programação, de uma base de dados de conhecimento do Criador de FAQ. O Criador de FAQ extrai automaticamente perguntas e respostas de conteúdos semiestruturados, como FAQs, a partir de [origens de dados](../Concepts/data-sources-supported.md). O modelo da base de dados de conhecimento é definido no JSON enviado no corpo do pedido da API. 

Este início rápido chama as APIs do Criador de FAQ:
* [Criar KB](https://westus.dev.cognitive.microsoft.com/docs/services/5a93fcf85b4ccd136866eb37/operations/5ac266295b4ccd1554da75ff)
* [Obter Detalhes da Operação](https://westus.dev.cognitive.microsoft.com/docs/services/5a93fcf85b4ccd136866eb37/operations/operations_getoperationdetails)
* [Publicar](https://westus.dev.cognitive.microsoft.com/docs/services/5a93fcf85b4ccd136866eb37/operations/5ac266295b4ccd1554da75fe) 

## <a name="prerequisites"></a>Pré-requisitos

* A [**edição da Comunidade do Visual Studio**](https://www.visualstudio.com/downloads/) mais recente.
* Tem de ter um [serviço Criador de FAQ](../How-To/set-up-qnamaker-service-azure.md). Para obter a sua chave, selecione **Chaves** em **Gestão de Recursos** no seu dashboard. 

> [!NOTE] 
> Os ficheiros de solução completos estão disponíveis a partir do repositório do Github [**Azure-Samples/cognitive-services-qnamaker-csharp**](https://github.com/Azure-Samples/cognitive-services-qnamaker-csharp).

## <a name="create-a-knowledge-base-project"></a>Criar um projeto de base de dados de conhecimento

[!INCLUDE [Create Visual Studio Project](../../../../includes/cognitive-services-qnamaker-quickstart-csharp-create-project.md)] 

## <a name="add-the-required-dependencies"></a>Adicionar as dependências necessárias

Na parte superior de Program.cs, substitua a instrução using única pelas seguintes linhas para adicionar as dependências necessárias ao projeto:

[!code-csharp[Add the required dependencies](~/samples-qnamaker-csharp/documentation-samples/quickstarts/create-knowledge-base/QnaQuickstartCreateKnowledgebase/Program.cs?range=1-11 "Add the required dependencies")]

## <a name="add-the-required-constants"></a>Adicionar as constantes necessárias

Na parte superior da classe Programa, adicione as seguintes constantes para aceder ao Criador de FAQ:

[!code-csharp[Add the required constants](~/samples-qnamaker-csharp/documentation-samples/quickstarts/create-knowledge-base/QnaQuickstartCreateKnowledgebase/Program.cs?range=17-24 "Add the required constants")]

## <a name="add-the-kb-definition"></a>Adicionar a definição de KB

Depois das constantes, adicione a seguinte definição de KB:

[!code-csharp[Add the required constants](~/samples-qnamaker-csharp/documentation-samples/quickstarts/create-knowledge-base/QnaQuickstartCreateKnowledgebase/Program.cs?range=32-57 "Add the required constants")]

## <a name="add-supporting-functions-and-structures"></a>Adicionar funções e estruturas de suporte
Adicione o seguinte bloco de código dentro da classe Programa:

[!code-csharp[Add supporting functions and structures](~/samples-qnamaker-csharp/documentation-samples/quickstarts/create-knowledge-base/QnaQuickstartCreateKnowledgebase/Program.cs?range=62-82 "Add supporting functions and structures")]

## <a name="add-a-post-request-to-create-kb"></a>Adicionar um pedido POST para criar KB

O código seguinte faz um pedido HTTPS à API do Criador de FAQ para criar uma KB e recebe a resposta:

[!code-csharp[Add a POST request to create KB](~/samples-qnamaker-csharp/documentation-samples/quickstarts/create-knowledge-base/QnaQuickstartCreateKnowledgebase/Program.cs?range=91-105 "Add a POST request to create KB")]

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

[!code-csharp[Add GET request to determine creation status](~/samples-qnamaker-csharp/documentation-samples/quickstarts/create-knowledge-base/QnaQuickstartCreateKnowledgebase/Program.cs?range=159-170 "Add GET request to determine creation status")]

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

O método seguinte cria a KB e repete as verificações de estado.  É devolvido _create_ **ID da Operação** no campo do cabeçalho de resposta POST **Localização** e, em seguida, é utilizado como parte da rota no pedido GET. Uma vez que a criação da KB pode demorar algum tempo, terá de repetir as chamadas para verificar o estado até que o estado seja com êxito ou falhe. Quando a operação for concluída com êxito, o ID da BDC é devolvido em **resourceLocation**. 

[!code-csharp[Add CreateKB method](~/samples-qnamaker-csharp/documentation-samples/quickstarts/create-knowledge-base/QnaQuickstartCreateKnowledgebase/Program.cs?range=176-237 "Add CreateKB method")]

## <a name="add-the-createkb-method-to-main"></a>Adicionar o método CreateKB ao Main

Altere o método Main para chamar o método CreateKB:

[!code-csharp[Add CreateKB method](~/samples-qnamaker-csharp/documentation-samples/quickstarts/create-knowledge-base/QnaQuickstartCreateKnowledgebase/Program.cs?range=239-248 "Add CreateKB method")]

## <a name="build-and-run-the-program"></a>Criar e executar o programa

Crie e execute o programa. Enviará automaticamente o pedido à API do Criador de FAQ para criar a KB e, em seguida, irá consultar para obter os resultados a cada 30 segundos. Cada resposta é impressa na janela da consola.

Assim que a sua base de dados de conhecimento é criada, pode visualizá-la no seu Portal do Criador de FAQ, na página [My knowledge bases](https://www.qnamaker.ai/Home/MyServices) (As minhas bases de dados de conhecimento). 


[!INCLUDE [Clean up files and KB](../../../../includes/cognitive-services-qnamaker-quickstart-cleanup-resources.md)] 

## <a name="next-steps"></a>Passos Seguintes

> [!div class="nextstepaction"]
> [Referência à API REST do Criador de FAQ](https://westus.dev.cognitive.microsoft.com/docs/services/5a93fcf85b4ccd136866eb37/operations/5ac266295b4ccd1554da75ff)
