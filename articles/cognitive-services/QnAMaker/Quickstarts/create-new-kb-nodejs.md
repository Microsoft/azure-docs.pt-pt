---
title: 'Início rápido: Criar base de dados de conhecimento - REST, node. js - QnA Maker'
description: Este início rápido baseado em REST descreve a criação programática de uma base de dados de conhecimento do Criador de FAQ de exemplo, que será apresentada no Dashboard do Azure da sua conta da API dos Serviços Cognitivos.
services: cognitive-services
author: diberry
manager: nitinme
ms.service: cognitive-services
ms.subservice: qna-maker
ms.topic: quickstart
ms.date: 02/04/2019
ms.author: diberry
ms.openlocfilehash: e2ab1a37115ab65cddbb65d28402d431a615b5f1
ms.sourcegitcommit: 36c50860e75d86f0d0e2be9e3213ffa9a06f4150
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 05/16/2019
ms.locfileid: "65788581"
---
# <a name="quickstart-create-a-knowledge-base-in-qna-maker-using-nodejs"></a>Início rápido: Criar uma base de dados de conhecimento no QnA Maker com node. js

Este início rápido descreve a criação e publicação, através de programação, de uma base de dados de conhecimento do Criador de FAQ. O Criador de FAQ extrai automaticamente perguntas e respostas de conteúdos semiestruturados, como FAQs, a partir de [origens de dados](../Concepts/data-sources-supported.md). O modelo da base de dados de conhecimento é definido no JSON enviado no corpo do pedido da API. 

Este início rápido chama as APIs do Criador de FAQ:
* [Criar KB](https://go.microsoft.com/fwlink/?linkid=2092179)
* [Obter Detalhes da Operação](https://docs.microsoft.com/rest/api/cognitiveservices/qnamaker/operations/getdetails)

## <a name="prerequisites"></a>Pré-requisitos

* [Node.js 6+](https://nodejs.org/en/download/)
* Tem de ter um [serviço Criador de FAQ](../How-To/set-up-qnamaker-service-azure.md). Para obter a sua chave, selecione **Chaves** em **Gestão de Recursos** no seu dashboard. 

[!INCLUDE [Code is available in Azure-Samples GitHub repo](../../../../includes/cognitive-services-qnamaker-nodejs-repo-note.md)]

## <a name="create-a-knowledge-base-nodejs-file"></a>Criar um ficheiro Node.js de base de dados de conhecimento

Crie um ficheiro com o nome `create-new-knowledge-base.js`.

## <a name="add-the-required-dependencies"></a>Adicionar as dependências necessárias

Na parte superior do `create-new-knowledge-base.js`, adicione as seguintes linhas para adicionar as dependências necessárias ao projeto:

[!code-nodejs[Add the dependencies](~/samples-qnamaker-nodejs/documentation-samples/quickstarts/create-knowledge-base/create-new-knowledge-base.js?range=1-4 "Add the dependencies")]

## <a name="add-the-required-constants"></a>Adicionar as constantes necessárias
Depois das dependências necessárias anteriores, adicione as constantes necessárias para aceder ao Criador de FAQ. Substitua o valor da variável `subscriptionKey` pela sua chave do Criador de FAQ.

[!code-nodejs[Add the required constants](~/samples-qnamaker-nodejs/documentation-samples/quickstarts/create-knowledge-base/create-new-knowledge-base.js?range=10-19 "Add the required constants")]

## <a name="add-the-kb-model-definition"></a>Adicionar a definição de modelo de KB

Depois das constantes, adicione a seguinte definição de modelo de KB. Este modelo converte-se numa cadeia de carateres após a definição.

[!code-nodejs[Add the KB model definition](~/samples-qnamaker-nodejs/documentation-samples/quickstarts/create-knowledge-base/create-new-knowledge-base.js?range=21-51 "Add the KB model definition")]

## <a name="add-supporting-functions"></a>Adicionar funções de suporte

Em seguida, adicione as funções de suporte seguintes.

1. Adicione a seguinte função para imprimir JSON num formato legível:

   [!code-nodejs[Add supporting functions, step 1](~/samples-qnamaker-nodejs/documentation-samples/quickstarts/create-knowledge-base/create-new-knowledge-base.js?range=53-56 "Add supporting functions, step 1")]

2. Adicione a seguinte função para gerir a resposta HTTP:

   [!code-nodejs[Add supporting functions, step 2](~/samples-qnamaker-nodejs/documentation-samples/quickstarts/create-knowledge-base/create-new-knowledge-base.js?range=58-80 "Add supporting functions, step 2")]

## <a name="add-functions-to-create-kb"></a>Adicionar funções para criar KB

Adicione as seguintes funções para fazer um pedido POST de HTTP para criar a base de dados de conhecimento. A `Ocp-Apim-Subscription-Key` é a chave de serviço do Criador de FAQ utilizada para autenticação. 

[!code-nodejs[POST Request to API](~/samples-qnamaker-nodejs/documentation-samples/quickstarts/create-knowledge-base/create-new-knowledge-base.js?range=82-109 "POST Request to API")]

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

## <a name="add-functions-to-determine-creation-status"></a>Adicionar funções para determinar o estado de criação

Adicione a função seguinte para fazer um pedido GET de HTTP para verificar o estado da operação. A `Ocp-Apim-Subscription-Key` é a chave de serviço do Criador de FAQ utilizada para autenticação. 

[!code-nodejs[Determine creation status](~/samples-qnamaker-nodejs/documentation-samples/quickstarts/create-knowledge-base/create-new-knowledge-base.js?range=112-135 "Determine creation status")]

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


## <a name="add-create-kb-function"></a>Adicionar função create-kb

A função seguinte é a função principal, que cria a KB e repete as verificações de estado. É devolvido _create_ **ID da Operação** no campo do cabeçalho de resposta POST **Localização** e, em seguida, é utilizado como parte da rota no pedido GET. Uma vez que a criação da KB pode demorar algum tempo, terá de repetir as chamadas para verificar o estado até que o estado seja com êxito ou falhe.

[!code-nodejs[Add create-kb function](~/samples-qnamaker-nodejs/documentation-samples/quickstarts/create-knowledge-base/create-new-knowledge-base.js?range=137-167 "Add create-kb function")]

## <a name="run-the-program"></a>Execute o programa

Introduza o comando seguinte numa linha de comandos para executar o programa. Enviará o pedido à API do Criador de FAQ para criar a KB e, em seguida, irá consultar para obter os resultados a cada 30 segundos. Cada resposta é impressa na janela da consola.

```bash
node create-new-knowledge-base.js
```

Assim que a sua base de dados de conhecimento é criada, pode visualizá-la no seu Portal do Criador de FAQ, na página [My knowledge bases](https://www.qnamaker.ai/Home/MyServices) (As minhas bases de dados de conhecimento). 

[!INCLUDE [Clean up files and KB](../../../../includes/cognitive-services-qnamaker-quickstart-cleanup-resources.md)] 

## <a name="next-steps"></a>Passos Seguintes

> [!div class="nextstepaction"]
> [Referência à API REST do Criador de FAQ](https://go.microsoft.com/fwlink/?linkid=2092179)
