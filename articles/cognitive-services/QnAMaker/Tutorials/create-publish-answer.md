---
title: Criar, publicar, responder a
titleSuffix: QnA Maker - Azure Cognitive Services
description: Este tutorial baseado em REST orienta programaticamente a criação e publicação de uma base de dados de conhecimento e a resposta a uma pergunta a partir da mesma.
services: cognitive-services
author: diberry
manager: nitinme
ms.custom: seodec18
ms.service: cognitive-services
ms.subservice: qna-maker
ms.topic: tutorial
ms.date: 01/24/2019
ms.author: diberry
ms.openlocfilehash: dd307e4abf4145bad9e0be79d261abf98d7bb3b7
ms.sourcegitcommit: d89b679d20ad45d224fd7d010496c52345f10c96
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/12/2019
ms.locfileid: "57792366"
---
# <a name="tutorial-using-c-create-knowledge-base-then-answer-question"></a>Tutorial: Utilizar o C#, dados de conhecimento base, em seguida, responder a pergunta

Este tutorial orienta programaticamente a criação e publicação de uma base de dados de conhecimento (BDC) e a resposta a uma pergunta do cliente a partir da mesma. 

> [!div class="checklist"]
* Criar uma base de dados de conhecimento 
* Verificar estado de criação
* Preparar e publicar a base de dados de conhecimento
* Obter informações de pontos finais
* Utilizar o Curl para consultar a base de dados de conhecimento


Este início rápido chama as API do Criador de FAQ:

* [Criar uma base de dados de conhecimento (BDC)](https://westus.dev.cognitive.microsoft.com/docs/services/5a93fcf85b4ccd136866eb37/operations/5ac266295b4ccd1554da75ff)
* [Obter Detalhes da Operação](https://westus.dev.cognitive.microsoft.com/docs/services/5a93fcf85b4ccd136866eb37/operations/operations_getoperationdetails)
* [Obter detalhes da base de dados de conhecimento](https://westus.dev.cognitive.microsoft.com/docs/services/5a93fcf85b4ccd136866eb37/operations/knowledgebases_getknowledgebasedetails) 
* [Obter pontos finais da base de dados de conhecimento](https://westus.dev.cognitive.microsoft.com/docs/services/5a93fcf85b4ccd136866eb37/operations/endpointkeys_getendpointkeys)
* [Publicar](https://westus.dev.cognitive.microsoft.com/docs/services/5a93fcf85b4ccd136866eb37/operations/5ac266295b4ccd1554da75fe) 

## <a name="prerequisites"></a>Pré-requisitos

* A [**edição da Comunidade do Visual Studio**](https://www.visualstudio.com/downloads/) mais recente.
* Tem de ter um [serviço Criador de FAQ](../How-To/set-up-qnamaker-service-azure.md). Para obter a sua chave, selecione **Chaves** em **Gestão de Recursos** no seu dashboard. 

> [!NOTE] 
> O ficheiro de solução concluída (s) está disponíveis a partir da [ **Azure-amostras/cognitivos-services-qnamaker-csharp** repositório do GitHub](https://github.com/Azure-Samples/cognitive-services-qnamaker-csharp/tree/master/documentation-samples/tutorials/create-publish-answer-knowledge-base).

## <a name="create-a-knowledge-base-project"></a>Criar um projeto de base de dados de conhecimento

[!INCLUDE [Create Visual Studio Project](../../../../includes/cognitive-services-qnamaker-quickstart-csharp-create-project.md)] 

## <a name="add-the-required-dependencies"></a>Adicionar as dependências necessárias

Na parte superior de Program.cs, substitua a instrução _using_ única pelas seguintes linhas para adicionar as dependências necessárias ao projeto:

[!code-csharp[Add the required dependencies](~/samples-qnamaker-csharp/documentation-samples/tutorials/create-publish-answer-knowledge-base/QnaMakerQuickstart/Program.cs?range=1-11 "Add the required dependencies")]

## <a name="add-a-kbdetails-class"></a>Adicionar uma classe KBDetails
Adicione esta classe KBDetails entre os parênteses retos do Espaço de Nomes. Essa classe permite que a biblioteca NewtonSoft anule a serialização da resposta JSON numa classe C#.

[!code-csharp[Add a KBDetails class](~/samples-qnamaker-csharp/documentation-samples/tutorials/create-publish-answer-knowledge-base/QnaMakerQuickstart/Program.cs?range=15-26 "Add a KBDetails class")]

## <a name="add-the-required-constants"></a>Adicionar as constantes necessárias

Na parte superior da classe Programa, adicione as seguintes constantes para aceder ao Criador de FAQ:

[!code-csharp[Add the required constants](~/samples-qnamaker-csharp/documentation-samples/tutorials/create-publish-answer-knowledge-base/QnaMakerQuickstart/Program.cs?range=30-57 "Add the required constants")]

## <a name="add-the-kb-definition"></a>Adicionar a definição de KB

Depois das constantes, adicione a seguinte definição de modelo de BDC:

[!code-csharp[Add the KB definition](~/samples-qnamaker-csharp/documentation-samples/tutorials/create-publish-answer-knowledge-base/QnaMakerQuickstart/Program.cs?range=59-85 "Add the KB definition")]

## <a name="add-supporting-functions-and-structures"></a>Adicionar funções e estruturas de suporte
Adicione o seguinte bloco de código dentro da classe Programa:

[!code-csharp[Add supporting functions and structures](~/samples-qnamaker-csharp/documentation-samples/tutorials/create-publish-answer-knowledge-base/QnaMakerQuickstart/Program.cs?range=87-123 "Add supporting functions and structures")]

## <a name="add-a-post-request-to-create-kb"></a>Adicionar um pedido POST para criar KB

O código seguinte faz um pedido HTTPS à API do Criador de FAQ para criar uma KB e recebe a resposta:

[!code-csharp[Add a POST request to create KB](~/samples-qnamaker-csharp/documentation-samples/tutorials/create-publish-answer-knowledge-base/QnaMakerQuickstart/Program.cs?range=124-141 "Add a POST request to create KB")]

Esta chamada à API devolve uma resposta JSON, que inclui o ID da operação. Mais tarde, o programa utiliza o ID da operação para determinar se a BDC foi criada com êxito. 

```JSON
{
  "operationState": "NotStarted",
  "createdTimestamp": "2018-09-26T05:19:01Z",
  "lastActionTimestamp": "2018-09-26T05:19:01Z",
  "userId": "XXX9549466094e1cb4fd063b646e1ad6",
  "operationId": "YYYe12ff-5d04-4b73-b594-8575f9787963"
}
```

## <a name="add-get-request-to-determine-creation-status"></a>Adicionar pedido GET para determinar o estado de criação

Verifique o estado da operação de criação.

[!code-csharp[Add GET request to determine creation status](~/samples-qnamaker-csharp/documentation-samples/tutorials/create-publish-answer-knowledge-base/QnaMakerQuickstart/Program.cs?range=142-151 "Add GET request to determine creation status")]

Esta chamada à API devolve uma resposta JSON que inclui o estado da operação: 

```JSON
{
  "operationState": "Running",
  "createdTimestamp": "2018-09-26T05:22:53Z",
  "lastActionTimestamp": "2018-09-26T05:22:53Z",
  "userId": "XXX9549466094e1cb4fd063b646e1ad6",
  "operationId": "YYYe12ff-5d04-4b73-b594-8575f9787963"
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
  "operationId": "YYYe12ff-5d04-4b73-b594-8575f9787963"
}
```

## <a name="add-createkb-method"></a>Adicionar método CreateKB

O método seguinte encapsula as chamadas para criar a BDC e verificar o estado.  É devolvido _create_ **ID da Operação** no campo do cabeçalho de resposta POST **Localização** e, em seguida, é utilizado como parte da rota no pedido GET. Uma vez que a criação da KB pode demorar algum tempo, terá de repetir as chamadas para verificar o estado até que o estado seja com êxito ou falhe. Quando a operação for concluída com êxito, o ID da BDC é devolvido em **resourceLocation**. 

[!code-csharp[Add GET request to determine creation status](~/samples-qnamaker-csharp/documentation-samples/tutorials/create-publish-answer-knowledge-base/QnaMakerQuickstart/Program.cs?range=152-227 "Add GET request to determine creation status")]

## <a name="add-publish-method"></a>Adicionar método publish

Depois de a base de dados de conhecimento ter sido criada com êxito, publique a BDC. Talvez esperasse uma chamada a uma API de preparação. Isto não é necessário com esta versão. 

O código seguinte faz um pedido HTTPS à API do Criador de FAQ para publicar uma KB e recebe a resposta:

[!code-csharp[Add publish method](~/samples-qnamaker-csharp/documentation-samples/tutorials/create-publish-answer-knowledge-base/QnaMakerQuickstart/Program.cs?range=228-259 "Add publish method")]

A chamada à API devolve um estado 204 para uma publicação com êxito, sem qualquer conteúdo no corpo da resposta. O código de início rápido adiciona texto para respostas 204 para que possa ver o resultado.

Para qualquer outra resposta, a resposta é devolvida inalterada.

## <a name="generating-an-answer"></a>Gerar uma resposta
Para aceder à BDC para enviar uma pergunta e receber a melhor resposta, o programa precisa do _anfitrião de ponto final_ da API de detalhes da BDC e da _chave de ponto final primária_ da API de Pontos Finais. Esses métodos estão nas secções seguintes, juntamente com o método para gerar uma resposta. 

A tabela seguinte ilustra a forma como os dados são utilizados para construir o URI:

|Gerar o modelo de URI de resposta|
|--|
|https://**HOSTNAME**.azurewebsites.net/qnamaker/knowledgebases/**KBID**/generateAnswer|

O _ponto final primário_ é transmitido como um cabeçalho para autenticar o pedido para gerar uma resposta:

|Nome do cabeçalho|Valor do cabeçalho|
|--|--|
|Autorização|`Endpoint ` + **ponto final primário**<br>Exemplo: `Endpoint xxxxxxx`<br>Repare no espaço entre o texto de `Endpoint` e o valor do ponto final primário. 

O corpo do pedido tem de transmitir o JSON adequado:

```JSON
{
    question: "What languages does QnA Maker support?"
}
```

## <a name="get-kb-details"></a>Obter os detalhes da BDC
Adicione o método seguinte para obter os detalhes da BDC. Estes detalhes contêm o nome do anfitrião da BDC. O nome do anfitrião é o nome do serviço Web Criador de FAQ do Azure que introduziu quando criou o recurso do Criador de FAQ. 

[!code-csharp[Get KB Details](~/samples-qnamaker-csharp/documentation-samples/tutorials/create-publish-answer-knowledge-base/QnaMakerQuickstart/Program.cs?range=260-273 "Add publish method")]

Esta chamada à API devolve uma resposta JSON: 

```JSON
{
  "id": "ZZZ31e19-cba7-48d1-8594-5c4297ecc9c1",
  "hostName": "https://qnamaker-s0-s.azurewebsites.net",
  "lastAccessedTimestamp": "2018-10-11T18:10:05Z",
  "lastChangedTimestamp": "2018-10-11T18:09:37Z",
  "lastPublishedTimestamp": "2018-10-11T18:10:15Z",
  "name": "QnA Maker FAQ from quickstart",
  "userId": "AAAc3841df0b42cdb00f53a49d51a89c",
  "urls": [
    "https://docs.microsoft.com/en-in/azure/cognitive-services/qnamaker/faqs",
    "https://docs.microsoft.com/bot-framework/resources-bot-framework-faq"
  ],
  "sources": [
    "Custom Editorial"
  ]
}
```

## <a name="get-kb-endpoints"></a>Obter pontos finais da BDC
Adicione o método seguinte para obter os pontos finais primários do Criador de FAQ. Estes pontos finais não estão associados à BDC; são válidos para todas as BDCs associadas com as chaves de recurso do Criador de FAQ no portal do Azure.  

[!code-csharp[Get KB Endpoints](~/samples-qnamaker-csharp/documentation-samples/tutorials/create-publish-answer-knowledge-base/QnaMakerQuickstart/Program.cs?range=274-289 "Get KB Endpoints")]

Esta chamada à API devolve uma resposta JSON: 

```JSON
{
  "primaryEndpointKey": "AAAb5719-e2f7-4a33-937d-7a3b4736a1be",
  "secondaryEndpointKey": "BBBcba78-c1d2-4166-b98f-c77255aefaba",
  "installedVersion": "4.2.0",
  "lastStableVersion": "4.2.0"
}
```

## <a name="get-an-answer"></a>Obter uma resposta
Adicione o método seguinte para obter uma resposta à pergunta do utilizador. 

[!code-csharp[Get Answer](~/samples-qnamaker-csharp/documentation-samples/tutorials/create-publish-answer-knowledge-base/QnaMakerQuickstart/Program.cs?range=290-315 "Get Answer")]

Esta chamada à API devolve uma resposta JSON: 

```JSON
{
  "answers": [
    {
      "questions": [
        "Does QnA Maker support non-English languages?"
      ],
      "answer": "See more details about [supported languages](https://docs.microsoft.com/en-in/azure/cognitive-services/qnamaker/overview/languages-supported).\n\n\nIf you have content from multiple languages, be sure to create a separate service for each language.",
      "score": 82.19,
      "id": 11,
      "source": "https://docs.microsoft.com/en-in/azure/cognitive-services/qnamaker/faqs",
      "metadata": []
    }
  ]
}
```

## <a name="main-method"></a>Método main
O método main mostra as chamadas síncronas para criar, publicar e gerar a resposta. 

[!code-csharp[Main method](~/samples-qnamaker-csharp/documentation-samples/tutorials/create-publish-answer-knowledge-base/QnaMakerQuickstart/Program.cs?range=316-337 "Main method")]

## <a name="build-and-run-the-program"></a>Compilar e executar o programa

Compile e execute o programa. 

Assim que a sua base de dados de conhecimento é criada, pode visualizá-la no seu Portal do Criador de FAQ, na página [My knowledge bases](https://www.qnamaker.ai/Home/MyServices) (As minhas bases de dados de conhecimento). Quando souber como utilizar a API para gerar respostas, pode utilizá-la com qualquer linguagem ou arquitetura de pedidos HTTP. 

## <a name="next-steps"></a>Passos Seguintes

> [!div class="nextstepaction"]
> [Referência à API REST do Criador de FAQ](https://westus.dev.cognitive.microsoft.com/docs/services/5a93fcf85b4ccd136866eb37/operations/5ac266295b4ccd1554da75ff)
