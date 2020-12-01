---
title: 'Início Rápido: criar base de dados de conhecimento – REST, Java – Criador de FAQ'
description: Este quickstart baseado em Java REST acompanha-o através da criação de uma base de conhecimento qna Maker, programáticamente, que aparecerá no seu Painel Azure da sua conta API de Serviços Cognitivos.
ms.service: cognitive-services
ms.subservice: qna-maker
ms.date: 12/16/2019
ROBOTS: NOINDEX,NOFOLLOW
ms.custom: RESTCURL2020FEB27, devx-track-java
ms.topic: how-to
ms.openlocfilehash: 50433ee1e5a575ab671d562bfc3fe549b26fe00c
ms.sourcegitcommit: 9eda79ea41c60d58a4ceab63d424d6866b38b82d
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 11/30/2020
ms.locfileid: "96352308"
---
# <a name="quickstart-create-a-knowledge-base-in-qna-maker-using-java"></a>Início Rápido: criar uma base de dados de conhecimento no Criador de FAQ com o Java

Este início rápido descreve a criação, através de programação, de uma base de dados de conhecimento do Criador de FAQ. O Criador de FAQ extrai automaticamente perguntas e respostas de conteúdos semiestruturados, como FAQs, a partir de [origens de dados](../index.yml). O modelo da base de dados de conhecimento é definido no JSON enviado no corpo do pedido da API.

Este início rápido chama as API do Criador de FAQ:
* [Criar KB](/rest/api/cognitiveservices/qnamaker/knowledgebase/create)
* [Obter Detalhes da operação](/rest/api/cognitiveservices/qnamaker/operations/getdetails)

[Documentação de referência](/rest/api/cognitiveservices/qnamaker/knowledgebase)  |  [Amostra de Java](https://github.com/Azure-Samples/cognitive-services-qnamaker-java/blob/master/documentation-samples/quickstarts/create-knowledge-base/CreateKB.java)

[!INCLUDE [Custom subdomains notice](../../../../includes/cognitive-services-custom-subdomains-note.md)]

## <a name="prerequisites"></a>Pré-requisitos

* [Go 1.10.1](https://golang.org/dl/)
* Tem de ter um [serviço Criador de FAQ](../How-To/set-up-qnamaker-service-azure.md). Para recuperar a sua chave e ponto final (que inclui o nome do recurso), selecione **Quickstart** para o seu recurso no portal Azure.

O [código de amostra](https://github.com/Azure-Samples/cognitive-services-qnamaker-java/blob/master/documentation-samples/quickstarts/create-knowledge-base/CreateKB.java) está disponível no repo GitHub para qna Maker com Java.

## <a name="create-a-knowledge-base-file"></a>Criar um ficheiro de base de dados de conhecimento

Crie um ficheiro com o nome `CreateKB.java`

## <a name="add-the-required-dependencies"></a>Adicionar as dependências necessárias

Na parte superior do `CreateKB.java`, adicione as seguintes linhas para adicionar as dependências necessárias ao projeto:

:::code language="java" source="~/cognitive-services-quickstart-code/java/QnAMaker/rest/CreateKB.java" id="dependencies":::

## <a name="add-the-required-constants"></a>Adicionar as constantes necessárias
Depois das dependências obrigatórias anteriores, adicione as constantes obrigatórias à classe `CreateKB` para aceder ao Criador de FAQ.

Tem de ter um [serviço Criador de FAQ](../How-To/set-up-qnamaker-service-azure.md). Para recuperar a sua chave e o nome do recurso, selecione **Quickstart** no portal Azure para o seu recurso QnA Maker.

Defina os seguintes valores:

* `<your-qna-maker-subscription-key>` - A **chave** é uma cadeia de 32 caracteres e está disponível no portal Azure, no recurso QnA Maker, na página Quickstart. Esta chave não é a mesma que a chave final de previsão.
* `<your-resource-name>` - O seu **nome de recurso** é utilizado para a construção do URL de ponta de autoria para autoria, no formato de `https://YOUR-RESOURCE-NAME.cognitiveservices.azure.com` . Este nome de recurso não é o mesmo que o usado para consultar o ponto final de previsão.

Não precisa de adicionar a chaveta de segurança final para terminar a classe; ela encontra-se no fragmento de código final no fim deste início rápido.

:::code language="java" source="~/cognitive-services-quickstart-code/java/QnAMaker/rest/CreateKB.java" id="constants":::


## <a name="add-the-kb-model-definition-classes"></a>Adicionar as classes de definição de modelo de KB
Depois das constantes, adicione as seguintes classes e funções dentro da classe `CreateKB` para serializar o objeto de definição de modelo em JSON.

:::code language="java" source="~/cognitive-services-quickstart-code/java/QnAMaker/rest/CreateKB.java" id="model":::

## <a name="add-supporting-functions"></a>Adicionar funções de suporte

Em seguida, adicione as funções de suporte seguintes dentro da classe `CreateKB`.

1. Adicione a seguinte função para imprimir JSON num formato legível:

    :::code language="java" source="~/cognitive-services-quickstart-code/java/QnAMaker/rest/CreateKB.java" id="pretty":::

2. Adicione a seguinte classe para gerir a resposta HTTP:

    :::code language="java" source="~/cognitive-services-quickstart-code/java/QnAMaker/rest/CreateKB.java" id="response":::

3. Adicione o seguinte método para fazer um pedido POST às APIs do Criador de FAQ. A `Ocp-Apim-Subscription-Key` é a chave de serviço do Criador de FAQ utilizada para autenticação.

    :::code language="java" source="~/cognitive-services-quickstart-code/java/QnAMaker/rest/CreateKB.java" id="post":::

4. Adicione o seguinte método para fazer um pedido GET às APIs do Criador de FAQ.

    :::code language="java" source="~/cognitive-services-quickstart-code/java/QnAMaker/rest/CreateKB.java" id="get":::

## <a name="add-a-method-to-create-the-kb"></a>Adicione um método para criar o KB
Adicione o seguinte método para criar o KB ao chamar o método Post.

:::code language="java" source="~/cognitive-services-quickstart-code/java/QnAMaker/rest/CreateKB.java" id="create_kb":::

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

:::code language="java" source="~/cognitive-services-quickstart-code/java/QnAMaker/rest/CreateKB.java" id="get_status":::

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
O método principal cria o KB e, em seguida, consulta o estado. O ID de operação é devolvido no **campo** de cabeçalho de resposta DO POST, depois utilizado como parte da rota no pedido GET. O `while` loop retrição o estado se não estiver concluído.

:::code language="java" source="~/cognitive-services-quickstart-code/java/QnAMaker/rest/CreateKB.java" id="main":::

## <a name="compile-and-run-the-program"></a>Compilar e executar o programa

1. Certifique-se de que a biblioteca de gson está no diretório `./libs`. Na linha de comando, compile o `CreateKB.java` ficheiro:

    ```bash
    javac -cp ".;libs/*" CreateKB.java
    ```

2. Introduza o seguinte comando numa linha de comando para executar o programa. Enviará o pedido à API do Criador de FAQ para criar a KB e, em seguida, irá consultar para obter os resultados a cada 30 segundos. Cada resposta é impressa na janela da consola.

    ```bash
    java -cp ",;libs/*" CreateKB
    ```

Assim que a sua base de dados de conhecimento é criada, pode visualizá-la no seu Portal do Criador de FAQ, na página [My knowledge bases](https://www.qnamaker.ai/Home/MyServices) (As minhas bases de dados de conhecimento).

[!INCLUDE [Clean up files and KB](../../../../includes/cognitive-services-qnamaker-quickstart-cleanup-resources.md)]

## <a name="next-steps"></a>Passos seguintes

> [!div class="nextstepaction"]
> [Referência à API REST do Criador de FAQ](/rest/api/cognitiveservices/qnamaker4.0/knowledgebase)