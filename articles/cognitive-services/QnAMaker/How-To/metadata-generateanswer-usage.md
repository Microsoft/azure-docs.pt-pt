---
title: Metadados com GenerateAnswer API - QnA Maker
titleSuffix: Azure Cognitive Services
description: O QnA Maker permite-lhe adicionar metadados, sob a forma de pares chave/valor, aos seus pares de perguntas/respostas. Pode filtrar resultados para consultas do utilizador e armazenar informações adicionais que podem ser usadas em conversas de seguimento.
services: cognitive-services
manager: nitinme
ms.service: cognitive-services
ms.subservice: qna-maker
ms.topic: conceptual
ms.date: 11/09/2020
ms.custom: devx-track-js, devx-track-csharp
ms.openlocfilehash: 9d2100dbc2c5f24742a949778a1b7450bf303c5f
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/30/2021
ms.locfileid: "103232210"
---
# <a name="get-an-answer-with-the-generateanswer-api"></a>Obtenha uma resposta com a API GenerateAnswer

Para obter a resposta prevista à pergunta de um utilizador, utilize a API GenerateAnswer. Ao publicar uma base de conhecimento, pode ver informações sobre como utilizar esta API na página **Publicar.** Também pode configurar a API para filtrar respostas com base em etiquetas de metadados e testar a base de conhecimento a partir do ponto final com o parâmetro de cadeia de consulta de teste.

<a name="generateanswer-api"></a>

## <a name="get-answer-predictions-with-the-generateanswer-api"></a>Obtenha previsões de resposta com a API GenerateAnswer

Utiliza a [API GenerateAnswer](/rest/api/cognitiveservices/qnamakerruntime/runtime/generateanswer) no seu bot ou aplicação para consultar a sua base de conhecimentos com uma pergunta do utilizador, para obter a melhor correspondência dos pares de perguntas e respostas.

<a name="generateanswer-endpoint"></a>

## <a name="publish-to-get-generateanswer-endpoint"></a>Publicar para obter GenerateAnswer ponto final

Depois de publicar a sua base de conhecimentos, seja a partir do [portal QnA Maker,](https://www.qnamaker.ai)ou utilizando a [API,](/rest/api/cognitiveservices/qnamaker/knowledgebase/publish)poderá obter os detalhes do seu ponto final GenerateAnswer.

Para obter os detalhes do seu ponto final:
1. Inicie sessão em [https://www.qnamaker.ai](https://www.qnamaker.ai).
1. Nas **minhas bases de conhecimento,** selecione **Ver Código** para a sua base de conhecimentos.
    ![Screenshot das minhas bases de conhecimento](../media/qnamaker-how-to-metadata-usage/my-knowledge-bases.png)
1. Obtenha os seus detalhes do ponto final GenerateAnswer.

    # <a name="qna-maker-ga-stable-release"></a>[QnA Maker GA (lançamento estável)](#tab/v1)

    ![Screenshot dos detalhes do ponto final](../media/qnamaker-how-to-metadata-usage/view-code.png)

    # <a name="qna-maker-managed-preview-release"></a>[QnA Maker gerido (pré-visualização)](#tab/v2)

    ![Screenshot dos detalhes do ponto final geridos](../media/qnamaker-how-to-metadata-usage/view-code-managed.png)

    ---

Também pode obter os seus detalhes do ponto final a partir do **separador Definições** da sua base de conhecimento.

<a name="generateanswer-request"></a>

## <a name="generateanswer-request-configuration"></a>Configuração de pedido de GenerateAnswer

Ligue para GenerateAnswer com um pedido HTTP POST. Para obter um código de amostra que mostre como chamar GenerateAnswer, consulte os [quickstarts](../quickstarts/quickstart-sdk.md#generate-an-answer-from-the-knowledge-base).

O pedido do POST utiliza:

* Parâmetros [URI necessários](/rest/api/cognitiveservices/qnamakerruntime/runtime/train#uri-parameters)
* Propriedade de cabeçalho necessária, `Authorization` para segurança
* Propriedades [corporais necessárias.](/rest/api/cognitiveservices/qnamakerruntime/runtime/train#feedbackrecorddto)

O URL GenerateAnswer tem o seguinte formato:

```
https://{QnA-Maker-endpoint}/knowledgebases/{knowledge-base-ID}/generateAnswer
```

Lembre-se de definir a propriedade do cabeçalho HTTP `Authorization` com um valor da cadeia com um espaço de fuga `EndpointKey` e, em seguida, a chave de ponto final encontrada na página **Definições.**

Um exemplo de corpo JSON parece:

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

Saiba mais sobre [rankerType](../concepts/best-practices.md#choosing-ranker-type).

O JSON anterior solicitou apenas respostas que estejam a 30% ou acima da pontuação limiar.

<a name="generateanswer-response"></a>

## <a name="generateanswer-response-properties"></a>Gerar Propriedades de resposta de resímos

A [resposta](/rest/api/cognitiveservices/qnamakerruntime/runtime/generateanswer#successful-query) é um objeto JSON, incluindo todas as informações necessárias para mostrar a resposta e a próxima volta na conversa, se disponível.

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

O anterior JSON respondeu com uma resposta com uma pontuação de 38,5%.

## <a name="match-questions-only-by-text"></a>Apenas questões de correspondência, por texto

Por predefinição, o QnA Maker procura através de perguntas e respostas. Se quiser pesquisar apenas através de perguntas, para gerar uma resposta, utilize `RankerType=QuestionOnly` o corpo POST do pedido GenerateAnswer.

Pode pesquisar através do kb publicado, utilizando `isTest=false` ou no kb de teste utilizando `isTest=true` .

```json
{
  "question": "Hi",
  "top": 30,
  "isTest": true,
  "RankerType":"QuestionOnly"
}

```
## <a name="use-qna-maker-with-a-bot-in-c"></a>Use o Fabricante QnA com um bot em C #

A estrutura bot fornece acesso às propriedades do Fabricante QnA com a [API getAnswer](/dotnet/api/microsoft.bot.builder.ai.qna.qnamaker.getanswersasync#Microsoft_Bot_Builder_AI_QnA_QnAMaker_GetAnswersAsync_Microsoft_Bot_Builder_ITurnContext_Microsoft_Bot_Builder_AI_QnA_QnAMakerOptions_System_Collections_Generic_Dictionary_System_String_System_String__System_Collections_Generic_Dictionary_System_String_System_Double__):

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

O JSON anterior solicitou apenas respostas que estejam a 30% ou acima da pontuação limiar.

## <a name="use-qna-maker-with-a-bot-in-nodejs"></a>Use o Fabricante QnA com um bot em Node.js

A estrutura bot fornece acesso às propriedades do Fabricante QnA com a [API getAnswer](/javascript/api/botbuilder-ai/qnamaker?preserve-view=true&view=botbuilder-ts-latest#generateanswer-string---undefined--number--number-):

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

O JSON anterior solicitou apenas respostas que estejam a 30% ou acima da pontuação limiar.

## <a name="get-precise-answers-with-generateanswer-api"></a>Obtenha respostas precisas com a GenerateAnswer API

# <a name="qna-maker-ga-stable-release"></a>[QnA Maker GA (lançamento estável)](#tab/v1)

Oferecemos uma funcionalidade de resposta precisa apenas com a versão gerida pelo QnA Maker.

# <a name="qna-maker-managed-preview-release"></a>[QnA Maker gerido (pré-visualização)](#tab/v2)

O utilizador pode ativar [respostas precisas](../reference-precise-answering.md) ao utilizar o recurso gerido pelo QnA Maker. O parâmetro respostaSpanRequest tem de ser atualizado para o mesmo.

```json
{
    "question": "How long it takes to charge surface pro 4?",
    "top": 3,
    "answerSpanRequest": {
        "enable": true,
        "topAnswersWithSpan": 1
    }
}
```

Da mesma forma, os utilizadores podem optar por desativar respostas precisas não definindo o parâmetro respostaSpanRequest.

```json
{
    "question": "How long it takes to charge surface pro 4?",
    "top": 3
}
```
### <a name="bot-settings"></a>Configurações do bot

Se pretender configurar definições precisas de resposta para o seu serviço de bot, navegue para o recurso de serviço app para o seu bot. Em seguida, tem de atualizar as configurações adicionando a seguinte definição.

- EnablePreciseAnswer
- DisplayPreciseAnswerOnly

|Configuração do ecrã|EnablePreciseAnswer|DisplayPreciseAnswerOnly|
|:--|--|--|
|Apenas respostas precisas|true|true|
|Apenas respostas longas|false|false|
|Respostas longas e precisas|true|false|

---

## <a name="common-http-errors"></a>Erros http comuns

|Código|Explicação|
|:--|--|
|2xx|Com êxito|
|400|Os parâmetros do pedido são incorretos, o que significa que os parâmetros necessários estão em falta, mal formados ou muito grandes|
|400|O corpo do pedido está incorreto, o que significa que o JSON está desaparecido, mal formado ou muito grande.|
|401|Chave inválida|
|403|Proibido - não tem permissões corretas|
|404|KB não existe|
|410|Esta API é depreciada e já não está disponível|

## <a name="next-steps"></a>Passos seguintes

A página **publicar** também fornece informações para [gerar uma resposta](../Quickstarts/get-answer-from-knowledge-base-using-url-tool.md) com o Carteiro ou cURL.

> [!div class="nextstepaction"]
> [Obter análises da base de dados de conhecimento](../how-to/get-analytics-knowledge-base.md)
> [!div class="nextstepaction"]
> [Pontuação de confiança](../Concepts/confidence-score.md)
