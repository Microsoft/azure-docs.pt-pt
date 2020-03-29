---
title: 'Início Rápido: criar base de dados de conhecimento – REST, Java – Criador de FAQ'
description: Este início rápido baseado em REST do Java descreve a criação programática de uma base de dados de conhecimento do Criador de FAQ de exemplo, que será apresentada no Dashboard do Azure da sua conta da API dos Serviços Cognitivos.
ms.date: 12/16/2019
ROBOTS: NOINDEX,NOFOLLOW
ms.custom: RESTCURL2020FEB27
ms.topic: conceptual
ms.openlocfilehash: 90ab36389ceac2e8aad12332db433732525c62f5
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/28/2020
ms.locfileid: "78851830"
---
# <a name="quickstart-create-a-knowledge-base-in-qna-maker-using-java"></a>Início Rápido: criar uma base de dados de conhecimento no Criador de FAQ com o Java

Este início rápido descreve a criação, através de programação, de uma base de dados de conhecimento do Criador de FAQ. O Criador de FAQ extrai automaticamente perguntas e respostas de conteúdos semiestruturados, como FAQs, a partir de [origens de dados](../Concepts/knowledge-base.md). O modelo da base de dados de conhecimento é definido no JSON enviado no corpo do pedido da API.

Este início rápido chama as API do Criador de FAQ:
* [Criar KB](https://docs.microsoft.com/rest/api/cognitiveservices/qnamaker/knowledgebase/create)
* [Obter Detalhes da operação](https://docs.microsoft.com/rest/api/cognitiveservices/qnamaker/operations/getdetails)

[Documentação de referência](https://docs.microsoft.com/rest/api/cognitiveservices/qnamaker/knowledgebase) | [Amostra java](https://github.com/Azure-Samples/cognitive-services-qnamaker-java/blob/master/documentation-samples/quickstarts/create-knowledge-base/CreateKB.java)

[!INCLUDE [Custom subdomains notice](../../../../includes/cognitive-services-custom-subdomains-note.md)]

## <a name="prerequisites"></a>Pré-requisitos

* [Go 1.10.1](https://golang.org/dl/)
* Tem de ter um [serviço Criador de FAQ](../How-To/set-up-qnamaker-service-azure.md). Para recuperar a sua chave e ponto final (que inclui o nome do recurso), selecione **Quickstart** para o seu recurso no portal Azure.

O [código da amostra](https://github.com/Azure-Samples/cognitive-services-qnamaker-java/blob/master/documentation-samples/quickstarts/create-knowledge-base/CreateKB.java) está disponível no repo GitHub para O Fabricante qnA com Java.

## <a name="create-a-knowledge-base-file"></a>Criar um ficheiro de base de dados de conhecimento

Crie um ficheiro com o nome `CreateKB.java`

## <a name="add-the-required-dependencies"></a>Adicionar as dependências necessárias

Na parte superior do `CreateKB.java`, adicione as seguintes linhas para adicionar as dependências necessárias ao projeto:

[!code-java[Add the required dependencies](~/samples-qnamaker-java/documentation-samples/quickstarts/create-knowledge-base/CreateKB.java?range=1-5 "Add the required dependencies")]

## <a name="add-the-required-constants"></a>Adicionar as constantes necessárias
Depois das dependências obrigatórias anteriores, adicione as constantes obrigatórias à classe `CreateKB` para aceder ao Criador de FAQ.

Tem de ter um [serviço Criador de FAQ](../How-To/set-up-qnamaker-service-azure.md). Para recuperar o seu nome de chave e recursos, selecione **Quickstart** no portal Azure para o seu recurso QnA Maker.

Defina os seguintes valores:

* `<your-qna-maker-subscription-key>`- A **chave** é uma cadeia de caracteres de 32 caracteres e está disponível no portal Azure, no recurso QnA Maker, na página Quickstart. Isto não é o mesmo que a chave final da previsão.
* `<your-resource-name>`- O seu nome de **recurso** é utilizado para construir o `https://YOUR-RESOURCE-NAME.cognitiveservices.azure.com`URL de ponto final de autoria para autoria, no formato de . Este não é o mesmo URL usado para consultar o ponto final da previsão.

Não precisa de adicionar a chaveta de segurança final para terminar a classe; ela encontra-se no fragmento de código final no fim deste início rápido.

[!code-java[Add the required constants](~/samples-qnamaker-java/documentation-samples/quickstarts/create-knowledge-base/CreateKB.java?range=26-34 "Add the required constants")]


## <a name="add-the-kb-model-definition-classes"></a>Adicionar as classes de definição de modelo de KB
Depois das constantes, adicione as seguintes classes e funções dentro da classe `CreateKB` para serializar o objeto de definição de modelo em JSON.

[!code-java[Add the KB model definition classes](~/samples-qnamaker-java/documentation-samples/quickstarts/create-knowledge-base/CreateKB.java?range=36-80 "Add the KB model definition classes")]

## <a name="add-supporting-functions"></a>Adicionar funções de suporte

Em seguida, adicione as funções de suporte seguintes dentro da classe `CreateKB`.

1. Adicione a seguinte função para imprimir JSON num formato legível:

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

## <a name="add-a-main-method"></a>Adicione um método principal
O método principal cria o KB e, em seguida, consulta o estado. O ID de funcionamento é devolvido no campo de cabeçalho de resposta POST **Localização,** e depois utilizado como parte da rota no pedido GET. O `while` laço volta a tentar o estado se não estiver concluído.

[!code-java[Add main method](~/samples-qnamaker-java/documentation-samples/quickstarts/create-knowledge-base/CreateKB.java?range=152-191 "Add main method")]

## <a name="compile-and-run-the-program"></a>Compilar e executar o programa

1. Certifique-se de que a biblioteca de gson está no diretório `./libs`. Na linha de comandos, compile o ficheiro `CreateKB.java`:

    ```bash
    javac -cp ".;libs/*" CreateKB.java
    ```

2. Introduza o seguinte comando numa linha de comandos para executar o programa. Enviará o pedido à API do Criador de FAQ para criar a KB e, em seguida, irá consultar para obter os resultados a cada 30 segundos. Cada resposta é impressa na janela da consola.

    ```bash
    java -cp ",;libs/*" CreateKB
    ```

Assim que a sua base de dados de conhecimento é criada, pode visualizá-la no seu Portal do Criador de FAQ, na página [My knowledge bases](https://www.qnamaker.ai/Home/MyServices) (As minhas bases de dados de conhecimento).

[!INCLUDE [Clean up files and KB](../../../../includes/cognitive-services-qnamaker-quickstart-cleanup-resources.md)]

## <a name="next-steps"></a>Passos seguintes

> [!div class="nextstepaction"]
> [Referência à API REST do Criador de FAQ](https://go.microsoft.com/fwlink/?linkid=2092179)