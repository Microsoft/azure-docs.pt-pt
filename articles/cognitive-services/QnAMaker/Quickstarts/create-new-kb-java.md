---
title: 'Início Rápido: criar base de dados de conhecimento – REST, Java – Criador de FAQ'
titleSuffix: Azure Cognitive Services
description: Este início rápido baseado em REST do Java descreve a criação programática de uma base de dados de conhecimento do Criador de FAQ de exemplo, que será apresentada no Dashboard do Azure da sua conta da API dos Serviços Cognitivos.
services: cognitive-services
author: diberry
manager: nitinme
ms.service: cognitive-services
ms.subservice: qna-maker
ms.topic: quickstart
ms.date: 12/16/2019
ms.author: diberry
ms.openlocfilehash: bd2e12660894f51ae4606ce3b2766f6cff821f41
ms.sourcegitcommit: f4f626d6e92174086c530ed9bf3ccbe058639081
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 12/25/2019
ms.locfileid: "75447645"
---
# <a name="quickstart-create-a-knowledge-base-in-qna-maker-using-java"></a>Início Rápido: criar uma base de dados de conhecimento no Criador de FAQ com o Java

Este início rápido descreve a criação, através de programação, de uma base de dados de conhecimento do Criador de FAQ. O Criador de FAQ extrai automaticamente perguntas e respostas de conteúdos semiestruturados, como FAQs, a partir de [origens de dados](../Concepts/data-sources-supported.md). O modelo da base de dados de conhecimento é definido no JSON enviado no corpo do pedido da API.

Este início rápido chama as API do Criador de FAQ:
* [Criar KB](https://docs.microsoft.com/rest/api/cognitiveservices/qnamaker/knowledgebase/create)
* [Obter Detalhes da Operação](https://docs.microsoft.com/rest/api/cognitiveservices/qnamaker/operations/getdetails)

[Documentação de referência](https://docs.microsoft.com/rest/api/cognitiveservices/qnamaker/knowledgebase) | [exemplo de Java](https://github.com/Azure-Samples/cognitive-services-qnamaker-java/blob/master/documentation-samples/quickstarts/create-knowledge-base/CreateKB.java)

[!INCLUDE [Custom subdomains notice](../../../../includes/cognitive-services-custom-subdomains-note.md)]

## <a name="prerequisites"></a>Pré-requisitos

* [Go 1.10.1](https://golang.org/dl/)
* Tem de ter um [serviço Criador de FAQ](../How-To/set-up-qnamaker-service-azure.md). Para recuperar a chave e o ponto de extremidade (que inclui o nome do recurso), selecione **início rápido** para seu recurso no portal do Azure.

O [código de exemplo](https://github.com/Azure-Samples/cognitive-services-qnamaker-java/blob/master/documentation-samples/quickstarts/create-knowledge-base/CreateKB.java) está disponível no repositório GitHub para QnA Maker com Java.

## <a name="create-a-knowledge-base-file"></a>Criar um ficheiro de base de dados de conhecimento

Crie um ficheiro com o nome `CreateKB.java`

## <a name="add-the-required-dependencies"></a>Adicionar as dependências necessárias

Na parte superior de `CreateKB.java`, adicione as linhas seguintes para adicionar as dependências necessárias ao projeto:

[!code-java[Add the required dependencies](~/samples-qnamaker-java/documentation-samples/quickstarts/create-knowledge-base/CreateKB.java?range=1-5 "Add the required dependencies")]

## <a name="add-the-required-constants"></a>Adicionar as constantes necessárias
Depois das dependências obrigatórias anteriores, adicione as constantes obrigatórias à classe `CreateKB` para aceder ao Criador de FAQ.

Tem de ter um [serviço Criador de FAQ](../How-To/set-up-qnamaker-service-azure.md). Para recuperar a chave e o nome do recurso, selecione **início rápido** no portal do Azure para o recurso de QnA Maker.

Defina os seguintes valores:

* `<your-qna-maker-subscription-key>`-a **chave** é uma cadeia de caracteres de 32 caracteres e está disponível no portal do Azure, no recurso de QnA Maker, na página início rápido. Isso não é o mesmo que a chave de ponto de extremidade de previsão.
* `<your-resource-name>`-o **nome do recurso** é usado para construir a URL do ponto de extremidade de criação para criação, no formato de `https://YOUR-RESOURCE-NAME.cognitiveservices.azure.com`. Essa não é a mesma URL usada para consultar o ponto de extremidade de previsão.

Não precisa de adicionar a chaveta de segurança final para terminar a classe; ela encontra-se no fragmento de código final no fim deste início rápido.

[!code-java[Add the required constants](~/samples-qnamaker-java/documentation-samples/quickstarts/create-knowledge-base/CreateKB.java?range=26-34 "Add the required constants")]


## <a name="add-the-kb-model-definition-classes"></a>Adicionar as classes de definição de modelo de KB
Depois das constantes, adicione as seguintes classes e funções dentro da classe `CreateKB` para serializar o objeto de definição de modelo em JSON.

[!code-java[Add the KB model definition classes](~/samples-qnamaker-java/documentation-samples/quickstarts/create-knowledge-base/CreateKB.java?range=36-80 "Add the KB model definition classes")]

## <a name="add-supporting-functions"></a>Adicionar funções de suporte

Em seguida, adicione as funções de suporte seguintes dentro da classe `CreateKB`.

1. Adicione a função seguinte para imprimir JSON num formato legível:

    [!code-java[Add the PrettyPrint function](~/samples-qnamaker-java/documentation-samples/quickstarts/create-knowledge-base/CreateKB.java?range=82-87 "Add the KB model definition classes")]

2. Adicione a seguinte classe para gerir a resposta HTTP:

    [!code-java[Add class to manage the HTTP response](~/samples-qnamaker-java/documentation-samples/quickstarts/create-knowledge-base/CreateKB.java?range=89-97 "Add class to manage the HTTP response")]

3. Adicione o seguinte método para fazer um pedido POST às APIs do Criador de FAQ. A `Ocp-Apim-Subscription-Key` é a chave de serviço do Criador de FAQ utilizada para autenticação.

    [!code-java[Add POST method](~/samples-qnamaker-java/documentation-samples/quickstarts/create-knowledge-base/CreateKB.java?range=99-121 "Add POST method")]

4. Adicione o seguinte método para fazer um pedido GET às APIs do Criador de FAQ.

    [!code-java[Add GET method](~/samples-qnamaker-java/documentation-samples/quickstarts/create-knowledge-base/CreateKB.java?range=123-137 "Add GET method")]

## <a name="add-a-method-to-create-the-kb"></a>Adicione um método para criar o KB
Adicione o seguinte método para criar o KB ao chamar o método Post.

[!code-java[Add CreateKB method](~/samples-qnamaker-java/documentation-samples/quickstarts/create-knowledge-base/CreateKB.java?range=139-144 "Add CreateKB method")]

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

## <a name="add-a-method-to-get-status"></a>Adicione um método para obter o estado
Adicione o seguinte método para verificar o estado de criação.

[!code-java[Add GetStatus method](~/samples-qnamaker-java/documentation-samples/quickstarts/create-knowledge-base/CreateKB.java?range=146-150 "Add GetStatus method")]

Repita a chamada até obter êxito ou falhar:

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

## <a name="add-a-main-method"></a>Adicione um método principal
O método principal cria o KB e, em seguida, consulta o estado. A ID da operação é retornada no **local**do campo do cabeçalho de resposta post e, em seguida, usada como parte da rota na solicitação get. O loop de `while` tenta novamente o status se ele não for concluído.

[!code-java[Add main method](~/samples-qnamaker-java/documentation-samples/quickstarts/create-knowledge-base/CreateKB.java?range=152-191 "Add main method")]

## <a name="compile-and-run-the-program"></a>Compilar e executar o programa

1. Certifique-se de que a biblioteca de gson está no diretório `./libs`. Na linha de comandos, compile o ficheiro `CreateKB.java`:

    ```bash
    javac -cp ".;libs/*" CreateKB.java
    ```

2. Introduza o comando seguinte numa linha de comandos para executar o programa. Enviará o pedido à API do Criador de FAQ para criar a KB e, em seguida, irá consultar para obter os resultados a cada 30 segundos. Cada resposta é impressa na janela da consola.

    ```bash
    java -cp ",;libs/*" CreateKB
    ```

Assim que a sua base de dados de conhecimento é criada, pode visualizá-la no seu Portal do Criador de FAQ, na página [My knowledge bases](https://www.qnamaker.ai/Home/MyServices) (As minhas bases de dados de conhecimento).

[!INCLUDE [Clean up files and KB](../../../../includes/cognitive-services-qnamaker-quickstart-cleanup-resources.md)]

## <a name="next-steps"></a>Passos seguintes

> [!div class="nextstepaction"]
> [Referência à API REST do Criador de FAQ](https://go.microsoft.com/fwlink/?linkid=2092179)
