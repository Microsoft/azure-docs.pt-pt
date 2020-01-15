---
title: Metadados com API GenerateAnswer-QnA Maker
titleSuffix: Azure Cognitive Services
description: QnA Maker permite que você adicione metadados, na forma de pares chave/valor, aos seus conjuntos de perguntas/respostas. Você pode filtrar os resultados para consultas de usuário e armazenar informações adicionais que podem ser usadas em conversas de acompanhamento.
services: cognitive-services
author: diberry
manager: nitinme
ms.service: cognitive-services
ms.subservice: qna-maker
ms.topic: conceptual
ms.date: 11/22/2019
ms.author: diberry
ms.openlocfilehash: e84a6c93ad8757b302670af202f9d4b407f3ef57
ms.sourcegitcommit: 49e14e0d19a18b75fd83de6c16ccee2594592355
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 01/14/2020
ms.locfileid: "75945305"
---
# <a name="get-an-answer-with-the-generateanswer-api-and-metadata"></a>Obtenha uma resposta com a API e os metadados do GenerateAnswer

Para obter a resposta prevista para a pergunta de um usuário, use a API GenerateAnswer. Ao publicar uma base de dados de conhecimento, você pode ver informações sobre como usar essa API na página **publicar** . Você também pode configurar a API para filtrar as respostas com base nas marcas de metadados e testar a base de dados de conhecimento do ponto de extremidade com o parâmetro de cadeia de caracteres de consulta de teste.

QnA Maker permite adicionar metadados, na forma de pares de chave e valor, aos seus conjuntos de perguntas e respostas. Você pode usar essas informações para filtrar os resultados para consultas de usuário e armazenar informações adicionais que podem ser usadas em conversas de acompanhamento. Para obter mais informações, consulte [base de dados de conhecimento](../Concepts/knowledge-base.md).

<a name="qna-entity"></a>

## <a name="store-questions-and-answers-with-a-qna-entity"></a>Armazenar perguntas e respostas com uma entidade QnA

É importante entender como QnA Maker armazena os dados de pergunta e resposta. A ilustração a seguir mostra uma entidade QnA:

![Ilustração de uma entidade QnA](../media/qnamaker-how-to-metadata-usage/qna-entity.png)

Cada entidade QnA tem uma ID exclusiva e persistente. Você pode usar a ID para fazer atualizações em uma entidade QnA específica.

<a name="generateanswer-api"></a>

## <a name="get-answer-predictions-with-the-generateanswer-api"></a>Obter previsões de resposta com a API GenerateAnswer

Você usa a [API GenerateAnswer](https://docs.microsoft.com/rest/api/cognitiveservices/qnamakerruntime/runtime/generateanswer) em seu bot ou aplicativo para consultar sua base de dados de conhecimento com uma pergunta de usuário, para obter a melhor correspondência dos conjuntos de perguntas e respostas.

<a name="generateanswer-endpoint"></a>

## <a name="publish-to-get-generateanswer-endpoint"></a>Publicar para obter ponto de extremidade GenerateAnswer

Depois de publicar sua base de dados de conhecimento, seja no [portal de QnA Maker](https://www.qnamaker.ai)ou usando a [API](https://docs.microsoft.com/rest/api/cognitiveservices/qnamaker/knowledgebase/publish), você pode obter os detalhes do seu ponto de extremidade GenerateAnswer.

Para obter os detalhes do ponto de extremidade:
1. Inicie sessão em [https://www.qnamaker.ai](https://www.qnamaker.ai).
1. Em **minhas bases de dados de conhecimento**, selecione **Exibir código** para sua base de dados de conhecimento.
    ![captura de tela de minhas bases de dados de conhecimento](../media/qnamaker-how-to-metadata-usage/my-knowledge-bases.png)
1. Obtenha os detalhes do ponto de extremidade do GenerateAnswer.

    ![Captura de tela dos detalhes do ponto de extremidade](../media/qnamaker-how-to-metadata-usage/view-code.png)

Você também pode obter os detalhes do ponto de extremidade na guia **configurações** da sua base de dados de conhecimento.

<a name="generateanswer-request"></a>

## <a name="generateanswer-request-configuration"></a>Configuração de solicitação do GenerateAnswer

Você chama GenerateAnswer com uma solicitação HTTP POST. Para obter um exemplo de código que mostra como chamar GenerateAnswer, consulte os [guias de início rápido](../quickstarts/quickstart-sdk.md#generate-an-answer-from-the-knowledge-base).

A solicitação POST usa:

* [Parâmetros de URI](https://docs.microsoft.com/rest/api/cognitiveservices/qnamakerruntime/runtime/train#uri-parameters) necessários
* [Propriedade de cabeçalho](https://docs.microsoft.com/azure/cognitive-services/qnamaker/quickstarts/get-answer-from-knowledge-base-nodejs#add-a-post-request-to-send-question-and-get-an-answer)necessária, `Authorization`, para segurança
* [Propriedades do corpo](https://docs.microsoft.com/rest/api/cognitiveservices/qnamakerruntime/runtime/train#feedbackrecorddto)necessárias.

A URL GenerateAnswer tem o seguinte formato:

```
https://{QnA-Maker-endpoint}/knowledgebases/{knowledge-base-ID}/generateAnswer
```

Lembre-se de definir a propriedade do cabeçalho HTTP de `Authorization` com um valor da cadeia de caracteres `EndpointKey` com um espaço à direita, depois a chave do ponto de extremidade encontrada na página **configurações** .

Um exemplo de corpo JSON é semelhante a:

```json
{
    "question": "qna maker and luis",
    "top": 6,
    "isTest": true,
    "scoreThreshold": 30,
    "rankerType": "" // values: QuestionOnly
    "strictFilters": [
    {
        "name": "category",
        "value": "api"
    }],
    "userId": "sd53lsY="
}
```

Saiba mais sobre o [rankerType](../concepts/best-practices.md#choosing-ranker-type).

O JSON anterior solicitou apenas respostas com 30% ou acima da Pontuação de limite.

<a name="generateanswer-response"></a>

## <a name="generateanswer-response-properties"></a>Propriedades de resposta do GenerateAnswer

A [resposta](https://docs.microsoft.com/rest/api/cognitiveservices/qnamakerruntime/runtime/generateanswer#successful-query) é um objeto JSON, incluindo todas as informações de que você precisa para exibir a resposta e o próximo ativar a conversa, se disponível.

```json
{
    "answers": [
        {
            "score": 38.54820341616869,
            "Id": 20,
            "answer": "There is no direct integration of LUIS with QnA Maker. But, in your bot code, you can use LUIS and QnA Maker together. [View a sample bot](https://github.com/Microsoft/BotBuilder-CognitiveServices/tree/master/Node/samples/QnAMaker/QnAWithLUIS)",
            "source": "Custom Editorial",
            "questions": [
                "How can I integrate LUIS with QnA Maker?"
            ],
            "metadata": [
                {
                    "name": "category",
                    "value": "api"
                }
            ]
        }
    ]
}
```

O JSON anterior respondeu com uma resposta com uma pontuação de 38,5%.

## <a name="use-qna-maker-with-a-bot-in-c"></a>Usar QnA Maker com um bot emC#

A estrutura de bot fornece acesso às propriedades do QnA Maker com a [API getrespondi](https://docs.microsoft.com/dotnet/api/microsoft.bot.builder.ai.qna.qnamaker.getanswersasync?view=botbuilder-dotnet-stable#Microsoft_Bot_Builder_AI_QnA_QnAMaker_GetAnswersAsync_Microsoft_Bot_Builder_ITurnContext_Microsoft_Bot_Builder_AI_QnA_QnAMakerOptions_System_Collections_Generic_Dictionary_System_String_System_String__System_Collections_Generic_Dictionary_System_String_System_Double__):

```csharp
using Microsoft.Bot.Builder.AI.QnA;
var metadata = new Microsoft.Bot.Builder.AI.QnA.Metadata();
var qnaOptions = new QnAMakerOptions();

metadata.Name = Constants.MetadataName.Intent;
metadata.Value = topIntent;
qnaOptions.StrictFilters = new Microsoft.Bot.Builder.AI.QnA.Metadata[] { metadata };
qnaOptions.Top = Constants.DefaultTop;
qnaOptions.ScoreThreshold = 0.3F;
var response = await _services.QnAServices[QnAMakerKey].GetAnswersAsync(turnContext, qnaOptions);
```

O JSON anterior solicitou apenas respostas com 30% ou acima da Pontuação de limite.

O bot de suporte tem [um exemplo](https://github.com/microsoft/BotBuilder-Samples/blob/master/experimental/qnamaker-support/csharp_dotnetcore/Service/SupportBotService.cs#L418) com esse código.

## <a name="use-qna-maker-with-a-bot-in-nodejs"></a>Usar QnA Maker com um bot no node. js

A estrutura de bot fornece acesso às propriedades do QnA Maker com a [API getrespondi](https://docs.microsoft.com/javascript/api/botbuilder-ai/qnamaker?view=botbuilder-ts-latest#generateanswer-string---undefined--number--number-):

```javascript
const { QnAMaker } = require('botbuilder-ai');
this.qnaMaker = new QnAMaker(endpoint);

// Default QnAMakerOptions
var qnaMakerOptions = {
    ScoreThreshold: 0.30,
    Top: 3
};
var qnaResults = await this.qnaMaker.getAnswers(stepContext.context, qnaMakerOptions);
```

O JSON anterior solicitou apenas respostas com 30% ou acima da Pontuação de limite.

O bot de suporte tem [um exemplo](https://github.com/microsoft/BotBuilder-Samples/blob/master/experimental/qnamaker-activelearning/javascript_nodejs/Helpers/dialogHelper.js#L36) com esse código.

<a name="metadata-example"></a>

## <a name="use-metadata-to-filter-answers-by-custom-metadata-tags"></a>Usar metadados para filtrar respostas por marcas de metadados personalizadas

A adição de metadados permite filtrar as respostas por essas marcas de metadados. Adicione a coluna de metadados do menu de **Opções de exibição** . Adicione metadados à sua base de dados de conhecimento selecionando o ícone de **+** de metadados para adicionar um par de metadados. Esse par consiste em uma chave e um valor.

![Captura de tela da adição de metadados](../media/qnamaker-how-to-metadata-usage/add-metadata.png)

<a name="filter-results-with-strictfilters-for-metadata-tags"></a>

## <a name="filter-results-with-strictfilters-for-metadata-tags"></a>Filtrar resultados com strictFilters para marcas de metadados

Considere a pergunta do usuário "quando este hotel é fechado?", em que a intenção é implícita para o restaurante "Paradise".

Como os resultados são necessários apenas para o restaurante "Paradise", você pode definir um filtro na chamada GenerateAnswer nos metadados "nome do restaurante". O exemplo a seguir mostra isso:

```json
{
    "question": "When does this hotel close?",
    "top": 1,
    "strictFilters": [
      {
        "name": "restaurant",
        "value": "paradise"
      }]
}
```

<a name="keep-context"></a>

## <a name="use-question-and-answer-results-to-keep-conversation-context"></a>Usar resultados de perguntas e respostas para manter o contexto da conversa

A resposta para o GenerateAnswer contém as informações de metadados correspondentes do conjunto de perguntas e respostas correspondentes. Você pode usar essas informações em seu aplicativo cliente para armazenar o contexto da conversa anterior para uso em conversas posteriores.

```json
{
    "answers": [
        {
            "questions": [
                "What is the closing time?"
            ],
            "answer": "10.30 PM",
            "score": 100,
            "id": 1,
            "source": "Editorial",
            "metadata": [
                {
                    "name": "restaurant",
                    "value": "paradise"
                },
                {
                    "name": "location",
                    "value": "secunderabad"
                }
            ]
        }
    ]
}
```

## <a name="match-questions-only-by-text"></a>Somente correspondência de perguntas, por texto

Por padrão, QnA Maker pesquisa perguntas e respostas. Se você quiser pesquisar apenas as perguntas, para gerar uma resposta, use o `RankerType=QuestionOnly` no corpo da POSTAgem da solicitação GenerateAnswer.

Você pode pesquisar os KB publicados, usando `isTest=false`ou na KB de teste usando `isTest=true`.

```json
{
  "question": "Hi",
  "top": 30,
  "isTest": true,
  "RankerType":"QuestionOnly"
}
```

## <a name="common-http-errors"></a>Erros comuns de HTTP

|Código|Explicação|
|:--|--|
|2xx|Êxito|
|400|parâmetros do pedido estão incorretos, que significa que os parâmetros necessários estão em falta, com formato incorreto ou é demasiado grande|
|400|corpo do pedido está incorreto, que significa que o JSON é demasiado grande, com formato incorreto ou em falta|
|401|Chave inválida|
|403|Proibido - não tem permissões corretas|
|404|Não existe KB|
|410|Esta API foi preterida e não está mais disponível|

## <a name="next-steps"></a>Passos seguintes

A página **publicar** também fornece informações para [gerar uma resposta com o](../Quickstarts/get-answer-from-knowledge-base-using-url-tool.md) postmaster ou a ondulação.

> [!div class="nextstepaction"]
> [Criar um bot da base de dados de conhecimento](../tutorials/integrate-qnamaker-luis.md)
