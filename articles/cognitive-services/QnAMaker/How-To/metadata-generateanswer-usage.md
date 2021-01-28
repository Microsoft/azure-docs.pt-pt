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
ms.openlocfilehash: f92b8c4835e42e758085e0434ee926fbabce5a38
ms.sourcegitcommit: 2f9f306fa5224595fa5f8ec6af498a0df4de08a8
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 01/28/2021
ms.locfileid: "98936233"
---
# <a name="get-an-answer-with-the-generateanswer-api-and-metadata"></a>Obtenha uma resposta com a API generateAnswer e metadados

Para obter a resposta prevista à pergunta de um utilizador, utilize a API GenerateAnswer. Ao publicar uma base de conhecimento, pode ver informações sobre como utilizar esta API na página **Publicar.** Também pode configurar a API para filtrar respostas com base em etiquetas de metadados e testar a base de conhecimento a partir do ponto final com o parâmetro de cadeia de consulta de teste.

O QnA Maker permite-lhe adicionar metadados, sob a forma de pares de chaves e valor, aos seus pares de perguntas e respostas. Em seguida, pode utilizar estas informações para filtrar resultados para consultas do utilizador e para armazenar informações adicionais que podem ser usadas em conversas de seguimento. Para mais informações, consulte [a Base de Conhecimento.](../index.yml)

<a name="qna-entity"></a>

## <a name="store-questions-and-answers-with-a-qna-entity"></a>Armazenar perguntas e respostas com uma entidade QnA

É importante entender como a QnA Maker armazena os dados de perguntas e respostas. A seguinte ilustração mostra uma entidade QnA:

![Ilustração de uma entidade QnA](../media/qnamaker-how-to-metadata-usage/qna-entity.png)

Cada entidade QnA tem um ID único e persistente. Pode utilizar o ID para fazer atualizações a uma determinada entidade QnA.

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

<a name="metadata-example"></a>

## <a name="use-metadata-to-filter-answers-by-custom-metadata-tags"></a>Utilize metadados para filtrar respostas por etiquetas de metadados personalizados

A adição de metadados permite filtrar as respostas através destas etiquetas de metadados. Adicione a coluna de metadados no menu **Ver Opções.** Adicione metadados à sua base de conhecimento selecionando o ícone de metadados **+** para adicionar um par de metadados. Este par é composto por uma chave e um valor.

![Screenshot da adição de metadados](../media/qnamaker-how-to-metadata-usage/add-metadata.png)

<a name="filter-results-with-strictfilters-for-metadata-tags"></a>

## <a name="filter-results-with-strictfilters-for-metadata-tags"></a>Filtrar resultados com filtros rigorosos para tags de metadados

Considere a pergunta do utilizador "Quando é que este hotel fecha?", onde a intenção está implícita para o restaurante "Paradise".

Como os resultados são necessários apenas para o restaurante "Paradise", pode definir um filtro na chamada GenerateAnswer nos metadados "Nome do Restaurante". O exemplo a seguir mostra o seguinte:

```json
{
    "question": "When does this hotel close?",
    "top": 1,
    "strictFilters": [ { "name": "restaurant", "value": "paradise"}]
}
```

### <a name="logical-and-by-default"></a>Lógica E por defeito

Para combinar vários filtros de metadados na consulta, adicione os filtros de metadados adicionais à matriz da `strictFilters` propriedade. Por predefinição, os valores são logicamente combinados (E). Uma combinação lógica requer todos os filtros para combinar com os pares QnA para que o par seja devolvido na resposta.

Isto equivale a usar o `strictFiltersCompoundOperationType` imóvel com o valor de `AND` .

### <a name="logical-or-using-strictfilterscompoundoperationtype-property"></a>Lógico OU usando propriedade rígida deFiltersCompoundOperationType

Ao combinar vários filtros de metadados, se estiver apenas preocupado com um ou alguns dos filtros correspondentes, utilize a `strictFiltersCompoundOperationType` propriedade com o valor de `OR` .

Isto permite que a sua base de conhecimento devolva respostas quando qualquer filtro corresponde, mas não devolve respostas que não têm metadados.

```json
{
    "question": "When do facilities in this hotel close?",
    "top": 1,
    "strictFilters": [
      { "name": "type","value": "restaurant"},
      { "name": "type", "value": "bar"},
      { "name": "type", "value": "poolbar"}
    ],
    "strictFiltersCompoundOperationType": "OR"
}
```

### <a name="metadata-examples-in-quickstarts"></a>Exemplos de metadados em arranques rápidos

Saiba mais sobre metadados no portal QnA Maker quickstart para metadados:
* [Autoria - adicione metadados ao par QnA](../quickstarts/add-question-metadata-portal.md#add-metadata-to-filter-the-answers)
* [Previsão de consulta - respostas de filtro por metadados](../quickstarts/get-answer-from-knowledge-base-using-url-tool.md)

<a name="keep-context"></a>

## <a name="use-question-and-answer-results-to-keep-conversation-context"></a>Use os resultados da pergunta e resposta para manter o contexto da conversação

A resposta ao GenerateAnswer contém as informações correspondentes de metadados do par de perguntas e respostas correspondentes. Pode utilizar esta informação na aplicação do seu cliente para armazenar o contexto da conversa anterior para utilização em conversas posteriores.

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

## <a name="next-steps"></a>Próximos passos

A página **publicar** também fornece informações para [gerar uma resposta](../Quickstarts/get-answer-from-knowledge-base-using-url-tool.md) com o Carteiro ou cURL.

> [!div class="nextstepaction"]
> [Obter análises da base de dados de conhecimento](../how-to/get-analytics-knowledge-base.md)
