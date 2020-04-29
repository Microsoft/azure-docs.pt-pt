---
title: Metadados com GenerateAnswer API - QnA Maker
titleSuffix: Azure Cognitive Services
description: O QnA Maker permite-lhe adicionar metadados, sob a forma de pares chave/valor, aos seus conjuntos de perguntas/respostas. Pode filtrar os resultados para as consultas do utilizador e armazenar informações adicionais que possam ser usadas em conversas de seguimento.
services: cognitive-services
author: diberry
manager: nitinme
ms.service: cognitive-services
ms.subservice: qna-maker
ms.topic: conceptual
ms.date: 03/31/2020
ms.author: diberry
ms.openlocfilehash: 9beb6dbbba1c5855b8bfa97fc02f50aa59225d78
ms.sourcegitcommit: 58faa9fcbd62f3ac37ff0a65ab9357a01051a64f
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/29/2020
ms.locfileid: "80474848"
---
# <a name="get-an-answer-with-the-generateanswer-api-and-metadata"></a>Obtenha uma resposta com a API GenerateAnswer e metadados

Para obter a resposta prevista para a pergunta de um utilizador, utilize a API GenerateAnswer. Ao publicar uma base de conhecimento, pode ver informações sobre como utilizar esta API na página **Publicar.** Também pode configurar a API para filtrar respostas com base em etiquetas de metadados e testar a base de conhecimento a partir do ponto final com o parâmetro de corda de consulta de teste.

O QnA Maker permite-lhe adicionar metadados, sob a forma de pares de chaves e valores, aos seus conjuntos de perguntas e respostas. Em seguida, pode utilizar estas informações para filtrar os resultados das consultas dos utilizadores e para armazenar informações adicionais que possam ser usadas em conversas de seguimento. Para mais informações, consulte a [base de Conhecimento.](../Concepts/knowledge-base.md)

<a name="qna-entity"></a>

## <a name="store-questions-and-answers-with-a-qna-entity"></a>Armazenar perguntas e respostas com uma entidade qnA

É importante entender como a QnA Maker armazena os dados de perguntas e respostas. A seguinte ilustração mostra uma entidade QnA:

![Ilustração de uma entidade QnA](../media/qnamaker-how-to-metadata-usage/qna-entity.png)

Cada entidade QnA tem um ID único e persistente. Pode utilizar o ID para fazer atualizações a uma determinada entidade qnA.

<a name="generateanswer-api"></a>

## <a name="get-answer-predictions-with-the-generateanswer-api"></a>Obtenha previsões de resposta com a API GenerateAnswer

Utiliza a [API GenerateAnswer](https://docs.microsoft.com/rest/api/cognitiveservices/qnamakerruntime/runtime/generateanswer) no seu bot ou aplicação para consultar a sua base de conhecimentos com uma pergunta do utilizador, para obter o melhor par dos conjuntos de perguntas e respostas.

<a name="generateanswer-endpoint"></a>

## <a name="publish-to-get-generateanswer-endpoint"></a>Publicar para obter ponto final Da GenerateAnswer

Depois de publicar a sua base de conhecimento, quer a partir do [portal QnA Maker,](https://www.qnamaker.ai)quer através da Utilização da [API,](https://docs.microsoft.com/rest/api/cognitiveservices/qnamaker/knowledgebase/publish)poderá obter os detalhes do seu ponto final GenerateAnswer.

Para obter os seus detalhes de ponto final:
1. Inicie sessão em [https://www.qnamaker.ai](https://www.qnamaker.ai).
1. Nas **minhas bases de conhecimento,** selecione **'Código de visão'** para a sua base de conhecimentos.
    ![Screenshot das minhas bases de conhecimento](../media/qnamaker-how-to-metadata-usage/my-knowledge-bases.png)
1. Obtenha os detalhes do ponto final da GenerateAnswer.

    ![Screenshot dos detalhes do ponto final](../media/qnamaker-how-to-metadata-usage/view-code.png)

Também pode obter os seus dados de ponto final a partir do separador **Definições** da sua base de conhecimentos.

<a name="generateanswer-request"></a>

## <a name="generateanswer-request-configuration"></a>Configuração de pedido GeraAnswer

Ligue para a GenerateAnswer com um pedido HTTP POST. Para obter um código de amostra que mostre como ligar para GenerateAnswer, consulte os [quickstarts](../quickstarts/quickstart-sdk.md#generate-an-answer-from-the-knowledge-base).

O pedido post utiliza:

* Parâmetros [URI necessários](https://docs.microsoft.com/rest/api/cognitiveservices/qnamakerruntime/runtime/train#uri-parameters)
* Propriedade de cabeçalho necessária, `Authorization`para segurança
* Propriedades [corporais necessárias.](https://docs.microsoft.com/rest/api/cognitiveservices/qnamakerruntime/runtime/train#feedbackrecorddto)

O URL GenerateAnswer tem o seguinte formato:

```
https://{QnA-Maker-endpoint}/knowledgebases/{knowledge-base-ID}/generateAnswer
```

Lembre-se de definir `Authorization` a propriedade do `EndpointKey` cabeçalho HTTP com um valor da corda com um espaço de trailing e, em seguida, a chave de ponto final encontrada na página **Definições.**

Um exemplo do corpo jSON parece:

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

Saiba mais sobre [o rankerType](../concepts/best-practices.md#choosing-ranker-type).

A JSON anterior solicitou apenas respostas que estão a 30% ou acima da pontuação limiar.

<a name="generateanswer-response"></a>

## <a name="generateanswer-response-properties"></a>Propriedades de resposta GenerateAnswer

A [resposta](https://docs.microsoft.com/rest/api/cognitiveservices/qnamakerruntime/runtime/generateanswer#successful-query) é um objeto JSON que inclui todas as informações necessárias para mostrar a resposta e a próxima volta na conversação, se disponível.

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

A anterior JSON respondeu com uma resposta com uma pontuação de 38,5%.

## <a name="use-qna-maker-with-a-bot-in-c"></a>Use o Fabricante QnA com um bot em C #

A estrutura bot proporciona acesso às propriedades do Fabricante qnA com a [getAnswer API:](https://docs.microsoft.com/dotnet/api/microsoft.bot.builder.ai.qna.qnamaker.getanswersasync?view=botbuilder-dotnet-stable#Microsoft_Bot_Builder_AI_QnA_QnAMaker_GetAnswersAsync_Microsoft_Bot_Builder_ITurnContext_Microsoft_Bot_Builder_AI_QnA_QnAMakerOptions_System_Collections_Generic_Dictionary_System_String_System_String__System_Collections_Generic_Dictionary_System_String_System_Double__)

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

A JSON anterior solicitou apenas respostas que estão a 30% ou acima da pontuação limiar.

## <a name="use-qna-maker-with-a-bot-in-nodejs"></a>Use O Fabricante QnA com um bot no Node.js

A estrutura bot proporciona acesso às propriedades do Fabricante qnA com a [getAnswer API:](https://docs.microsoft.com/javascript/api/botbuilder-ai/qnamaker?view=botbuilder-ts-latest#generateanswer-string---undefined--number--number-)

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

A JSON anterior solicitou apenas respostas que estão a 30% ou acima da pontuação limiar.

<a name="metadata-example"></a>

## <a name="use-metadata-to-filter-answers-by-custom-metadata-tags"></a>Utilize metadados para filtrar respostas através de etiquetas de metadados personalizados

Adicionar metadados permite filtrar as respostas através destas etiquetas de metadados. Adicione a coluna de metadados do menu **'Ver Opções'.** Adicione metadados à sua base de **+** conhecimentos selecionando o ícone dos metadados para adicionar um par de metadados. Este par consiste numa chave e num valor.

![Screenshot da adição de metadados](../media/qnamaker-how-to-metadata-usage/add-metadata.png)

<a name="filter-results-with-strictfilters-for-metadata-tags"></a>

## <a name="filter-results-with-strictfilters-for-metadata-tags"></a>Filtrar resultados com filtros rigorosos para etiquetas de metadados

Considere a pergunta do utilizador "Quando é que este hotel fecha?", onde a intenção está implícita para o restaurante "Paradise".

Como os resultados são necessários apenas para o restaurante "Paradise", pode definir um filtro na chamada GenerateAnswer nos metadados "Nome do Restaurante". O seguinte exemplo mostra isto:

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

## <a name="use-question-and-answer-results-to-keep-conversation-context"></a>Use resultados de perguntas e respostas para manter o contexto da conversação

A resposta ao GenerateAnswer contém as informações correspondentes dos metadados do conjunto de perguntas e respostas correspondentes. Pode utilizar esta informação na sua aplicação de cliente para armazenar o contexto da conversa anterior para utilização em conversas posteriores.

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

## <a name="match-questions-only-by-text"></a>Apenas perguntas de jogo, por texto

Por padrão, o QnA Maker procura através de perguntas e respostas. Se quiser pesquisar apenas através de perguntas, `RankerType=QuestionOnly` para gerar uma resposta, utilize o corpo post do pedido GenerateAnswer.

Pode pesquisar através do kb `isTest=false`publicado, utilizando , `isTest=true`ou no kb de teste utilizando .

```json
{
  "question": "Hi",
  "top": 30,
  "isTest": true,
  "RankerType":"QuestionOnly"
}
```

## <a name="common-http-errors"></a>Erros http comuns

|Código|Explicação|
|:--|--|
|2xx|Êxito|
|400|Os parâmetros do pedido são incorretos, o que significa que os parâmetros necessários estão em falta, mal formados ou muito grandes|
|400|O corpo do pedido está incorreto, o que significa que o JSON está desaparecido, mal formado ou muito grande|
|401|Chave inválida|
|403|Proibido - não tem permissões corretas|
|404|KB não existe|
|410|Esta API está depreciada e já não está disponível|

## <a name="next-steps"></a>Passos seguintes

A página **Publicar** também fornece informações para [gerar uma resposta](../Quickstarts/get-answer-from-knowledge-base-using-url-tool.md) com o Carteiro ou cURL.

> [!div class="nextstepaction"]
> [Criar um bot base de conhecimento](../tutorials/integrate-qnamaker-luis.md)
