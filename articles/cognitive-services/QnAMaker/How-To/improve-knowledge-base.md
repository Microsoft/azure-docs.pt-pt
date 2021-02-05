---
title: Ative Learning sugeriu perguntas - QnA Maker
description: Melhore a qualidade da sua base de conhecimento com aprendizagem ativa. Reveja, aceite ou rejeite, adicione sem remover ou alterar as questões existentes.
ms.service: cognitive-services
ms.subservice: qna-maker
ms.topic: conceptual
ms.date: 04/06/2020
ms.custom: devx-track-js, devx-track-csharp
ms.openlocfilehash: 3f2e8fef35095a007051999d806f2942089ae19a
ms.sourcegitcommit: 2817d7e0ab8d9354338d860de878dd6024e93c66
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 02/05/2021
ms.locfileid: "99584758"
---
# <a name="accept-active-learning-suggested-questions-in-the-knowledge-base"></a>Aceitar aprendizagem ativa sugerida questões na base de conhecimento


<a name="accept-an-active-learning-suggestion-in-the-knowledge-base"></a>

Ative Learning altera a Base de Conhecimento ou Serviço de Pesquisa depois de aprovar a sugestão, em seguida, guardar e treinar. Se aprovar a sugestão, será adicionada como uma pergunta alternativa.

## <a name="turn-on-active-learning"></a>Ativar a aprendizagem ativa

Para ver as perguntas sugeridas, deve [ligar a aprendizagem ativa](../concepts/active-learning-suggestions.md) para o seu recurso QnA Maker.

## <a name="view-suggested-questions"></a>Ver perguntas sugeridas

1. Para ver as perguntas sugeridas, na página base de conhecimento **editar,** selecione **Ver Opções** e, em seguida, selecione **Mostrar sugestões de aprendizagem ativa**. Esta opção será desativada se não houver sugestões presentes para qualquer um dos pares de perguntas e respostas.  

    [![Na secção Editar do portal, selecione Mostrar Sugestões para ver as novas alternativas de perguntas da aprendizagem ativa.](../media/improve-knowledge-base/show-suggestions-button.png)](../media/improve-knowledge-base/show-suggestions-button.png#lightbox)

1. Filtrar a base de conhecimento com pares de perguntas e respostas para mostrar apenas sugestões selecionando **Filtro por Sugestões**.

    [![Utilize o Filtro através de sugestões para visualizar apenas as alternativas de pergunta sugeridas pela aprendizagem ativa.](../media/improve-knowledge-base/filter-by-suggestions.png)](../media/improve-knowledge-base/filter-by-suggestions.png#lightbox)

1. Cada par QnA sugere as novas alternativas de pergunta com uma marca de verificação, `✔` para aceitar a pergunta ou um rejeitar as `x` sugestões. Selecione a marca de verificação para adicionar a pergunta.

    [![Selecione ou rejeite as alternativas de perguntas sugeridas pela aprendizagem ativa selecionando a marca de verificação verde ou a marca de eliminação vermelha.](../media/improve-knowledge-base/accept-active-learning-suggestions-small.png)](../media/improve-knowledge-base/accept-active-learning-suggestions.png#lightbox)

    Pode adicionar ou eliminar _todas as sugestões_ selecionando **Adicionar tudo** ou Rejeitar **tudo** na barra de ferramentas contextual.

1. **Selecione Guardar e Treinar** para guardar as alterações na base de conhecimentos.

1. **Selecione Publicar** para permitir que as alterações estejam disponíveis a partir da [API GenerateAnswer](metadata-generateanswer-usage.md#generateanswer-request-configuration).

    Quando 5 ou mais consultas semelhantes são agrupadas, a cada 30 minutos, o QnA Maker sugere as perguntas alternativas para aceitar ou rejeitar.


<a name="#score-proximity-between-knowledge-base-questions"></a>

## <a name="active-learning-suggestions-are-saved-in-the-exported-knowledge-base"></a>Sugestões de aprendizagem ativa são guardadas na base de conhecimento exportada

Quando a sua aplicação tem aprendizagem ativa ativa e exporta a app, a `SuggestedQuestions` coluna no ficheiro tsv retém os dados de aprendizagem ativa.

A `SuggestedQuestions` coluna é um objeto JSON de informação de feedback `autosuggested` implícito, e `usersuggested` explícito. Um exemplo deste objeto JSON para uma única questão submetida pelo utilizador `help` é:

```JSON
[
    {
        "clusterHead": "help",
        "totalAutoSuggestedCount": 1,
        "totalUserSuggestedCount": 0,
        "alternateQuestionList": [
            {
                "question": "help",
                "autoSuggestedCount": 1,
                "userSuggestedCount": 0
            }
        ]
    }
]
```

Ao reimportar esta aplicação, a aprendizagem ativa continua a recolher informações e a recomendar sugestões para a sua base de conhecimentos.


### <a name="architectural-flow-for-using-generateanswer-and-train-apis-from-a-bot"></a>Fluxo arquitetónico para usar APis de 2000 e de comboio de um bot

Um bot ou outra aplicação de cliente deve usar o seguinte fluxo arquitetónico para utilizar a aprendizagem ativa:

* Bot [obtém a resposta da base de conhecimento](#use-the-top-property-in-the-generateanswer-request-to-get-several-matching-answers) com a API GenerateAnswer, usando a propriedade para obter uma série de `top` respostas.

#### <a name="use-the-top-property-in-the-generateanswer-request-to-get-several-matching-answers"></a>Use a propriedade de topo no pedido generateAnswer para obter várias respostas correspondentes

Ao submeter uma pergunta ao QnA Maker para obter uma resposta, a `top` propriedade do corpo JSON define o número de respostas para devolver.

```json
{
    "question": "wi-fi",
    "isTest": false,
    "top": 3
}
```

* Bot determina feedback explícito:
    * Utilizando a sua própria [lógica de negócio personalizado,](#use-the-score-property-along-with-business-logic-to-get-list-of-answers-to-show-user)filtre pontuações baixas.
    * No bot ou na aplicação do cliente, mostrar a lista de possíveis respostas ao utilizador e obter a resposta selecionada do utilizador.
* Bot [envia resposta selecionada de volta ao QnA Maker](#bot-framework-sample-code) com a [API do comboio.](#train-api)


### <a name="use-the-score-property-along-with-business-logic-to-get-list-of-answers-to-show-user"></a>Use a propriedade de pontuação juntamente com a lógica de negócio para obter uma lista de respostas para mostrar ao utilizador

Quando a aplicação do cliente (como um chat bot) recebe a resposta, as 3 principais questões são devolvidas. Use a `score` propriedade para analisar a proximidade entre pontuações. Esta gama de proximidade é determinada pela sua própria lógica de negócio.

```json
{
    "answers": [
        {
            "questions": [
                "Wi-Fi Direct Status Indicator"
            ],
            "answer": "**Wi-Fi Direct Status Indicator**\n\nStatus bar icons indicate your current Wi-Fi Direct connection status:  \n\nWhen your device is connected to another device using Wi-Fi Direct, '$  \n\n+ *+ ' Wi-Fi Direct is displayed in the Status bar.",
            "score": 74.21,
            "id": 607,
            "source": "Bugbash KB.pdf",
            "metadata": []
        },
        {
            "questions": [
                "Wi-Fi - Connections"
            ],
            "answer": "**Wi-Fi**\n\nWi-Fi is a term used for certain types of Wireless Local Area Networks (WLAN). Wi-Fi communication requires access to a wireless Access Point (AP).",
            "score": 74.15,
            "id": 599,
            "source": "Bugbash KB.pdf",
            "metadata": []
        },
        {
            "questions": [
                "Turn Wi-Fi On or Off"
            ],
            "answer": "**Turn Wi-Fi On or Off**\n\nTurning Wi-Fi on makes your device able to discover and connect to compatible in-range wireless APs.  \n\n1.  From a Home screen, tap ::: Apps > e Settings .\n2.  Tap Connections > Wi-Fi , and then tap On/Off to turn Wi-Fi on or off.",
            "score": 69.99,
            "id": 600,
            "source": "Bugbash KB.pdf",
            "metadata": []
        }
    ]
}
```

## <a name="client-application-follow-up-when-questions-have-similar-scores"></a>Acompanhamento da aplicação do cliente quando as perguntas têm pontuações semelhantes

A aplicação do seu cliente exibe as perguntas com a opção de o utilizador selecionar _a única_ pergunta que mais representa a sua intenção.

Uma vez que o utilizador selecione uma das questões existentes, a aplicação do cliente envia a escolha do utilizador como feedback usando a API do Comboio da QnA Maker. Este feedback completa o ciclo de feedback de aprendizagem ativa.

## <a name="train-api"></a>API de Preparação

O feedback de aprendizagem ativa é enviado para o QnA Maker com o pedido de Train API POST. A assinatura da API é:

```http
POST https://<QnA-Maker-resource-name>.azurewebsites.net/qnamaker/knowledgebases/<knowledge-base-ID>/train
Authorization: EndpointKey <endpoint-key>
Content-Type: application/json
{"feedbackRecords": [{"userId": "1","userQuestion": "<question-text>","qnaId": 1}]}
```

|HTTP solicitar propriedade|Nome|Tipo|Objetivo|
|--|--|--|--|
|Parâmetro de rota URL|ID da base de conhecimento|string|O GUID para a sua base de conhecimento.|
|Subdomínio personalizado|Nome de recurso QnAMaker|string|O nome do recurso é usado como subdomínio personalizado para o seu QnA Maker. Isto está disponível na página Definições depois de publicar a base de conhecimento. Está listado `host` como.|
|Cabeçalho|Content-Type|string|O tipo de mídia do corpo enviado para a API. O valor predefinido é: `application/json`|
|Cabeçalho|Autorização|string|A sua chave de ponto final (EndpointKey xxxxxx-xxxx-xxxx-xxxx-xxxxx).|
|Corpo do Correio|Objeto JSON|JSON|O feedback do treino|

O corpo JSON tem várias configurações:

|Propriedade corporal JSON|Tipo|Objetivo|
|--|--|--|--|
|`feedbackRecords`|matriz|Lista de comentários.|
|`userId`|string|Identificação do utilizador da pessoa que aceita as perguntas sugeridas. O formato de ID do utilizador é da sua mente. Por exemplo, um endereço de e-mail pode ser um ID válido do utilizador na sua arquitetura. Opcional.|
|`userQuestion`|string|Texto exato da consulta do utilizador. Obrigatório.|
|`qnaID`|número|ID de pergunta, encontrado na [resposta GenerateAnswer](metadata-generateanswer-usage.md#generateanswer-response-properties). |

Um exemplo de corpo JSON parece:

```json
{
    "feedbackRecords": [
        {
            "userId": "1",
            "userQuestion": "<question-text>",
            "qnaId": 1
        }
    ]
}
```

Uma resposta bem sucedida devolve um estatuto de 204 e nenhum corpo de resposta JSON.

### <a name="batch-many-feedback-records-into-a-single-call"></a>Lote de muitos registos de feedback em uma única chamada

Na aplicação do lado do cliente, como um bot, você pode armazenar os dados, em seguida, enviar muitos registos em um único corpo JSON na `feedbackRecords` matriz.

Um exemplo de corpo JSON parece:

```json
{
    "feedbackRecords": [
        {
            "userId": "1",
            "userQuestion": "How do I ...",
            "qnaId": 1
        },
        {
            "userId": "2",
            "userQuestion": "Where is ...",
            "qnaId": 40
        },
        {
            "userId": "3",
            "userQuestion": "When do I ...",
            "qnaId": 33
        }
    ]
}
```



<a name="active-learning-is-saved-in-the-exported-apps-tsv-file"></a>

## <a name="bot-framework-sample-code"></a>Código de amostra de estrutura de bot

O seu código-quadro bot precisa de ligar para a API do comboio, se a consulta do utilizador deve ser usada para uma aprendizagem ativa. Há duas peças de código para escrever:

* Determinar se a consulta deve ser usada para a aprendizagem ativa
* Envie consulta de volta à API do Comboio da QnA Maker para aprendizagem ativa

Na [amostra Azure Bot,](https://github.com/microsoft/BotBuilder-Samples)ambas as atividades foram programadas.

### <a name="example-c-code-for-train-api-with-bot-framework-4x"></a>Código exemplo C# para API de comboio com Quadro Bot 4.x

O código que se segue ilustra como enviar informações de volta ao QnA Maker com a API do comboio.

```csharp
public class FeedbackRecords
{
    // <summary>
    /// List of feedback records
    /// </summary>
    [JsonProperty("feedbackRecords")]
    public FeedbackRecord[] Records { get; set; }
}

/// <summary>
/// Active learning feedback record
/// </summary>
public class FeedbackRecord
{
    /// <summary>
    /// User id
    /// </summary>
    public string UserId { get; set; }

    /// <summary>
    /// User question
    /// </summary>
    public string UserQuestion { get; set; }

    /// <summary>
    /// QnA Id
    /// </summary>
    public int QnaId { get; set; }
}

/// <summary>
/// Method to call REST-based QnAMaker Train API for Active Learning
/// </summary>
/// <param name="endpoint">Endpoint URI of the runtime</param>
/// <param name="FeedbackRecords">Feedback records train API</param>
/// <param name="kbId">Knowledgebase Id</param>
/// <param name="key">Endpoint key</param>
/// <param name="cancellationToken"> Cancellation token</param>
public async static void CallTrain(string endpoint, FeedbackRecords feedbackRecords, string kbId, string key, CancellationToken cancellationToken)
{
    var uri = endpoint + "/knowledgebases/" + kbId + "/train/";

    using (var client = new HttpClient())
    {
        using (var request = new HttpRequestMessage())
        {
            request.Method = HttpMethod.Post;
            request.RequestUri = new Uri(uri);
            request.Content = new StringContent(JsonConvert.SerializeObject(feedbackRecords), Encoding.UTF8, "application/json");
            request.Headers.Add("Authorization", "EndpointKey " + key);

            var response = await client.SendAsync(request, cancellationToken);
            await response.Content.ReadAsStringAsync();
        }
    }
}
```

### <a name="example-nodejs-code-for-train-api-with-bot-framework-4x"></a>Exemplo Node.js código para API de comboio com Bot Framework 4.x

O código que se segue ilustra como enviar informações de volta ao QnA Maker com a API do comboio.

```javascript
async callTrain(stepContext){

    var trainResponses = stepContext.values[this.qnaData];
    var currentQuery = stepContext.values[this.currentQuery];

    if(trainResponses.length > 1){
        var reply = stepContext.context.activity.text;
        var qnaResults = trainResponses.filter(r => r.questions[0] == reply);

        if(qnaResults.length > 0){

            stepContext.values[this.qnaData] = qnaResults;

            var feedbackRecords = {
                FeedbackRecords:[
                    {
                        UserId:stepContext.context.activity.id,
                        UserQuestion: currentQuery,
                        QnaId: qnaResults[0].id
                    }
                ]
            };

            // Call Active Learning Train API
            this.activeLearningHelper.callTrain(this.qnaMaker.endpoint.host, feedbackRecords, this.qnaMaker.endpoint.knowledgeBaseId, this.qnaMaker.endpoint.endpointKey);

            return await stepContext.next(qnaResults);
        }
        else{

            return await stepContext.endDialog();
        }
    }

    return await stepContext.next(stepContext.result);
}
```

## <a name="best-practices"></a>Melhores práticas

Para melhores práticas ao utilizar a aprendizagem ativa, consulte [as melhores práticas.](../Concepts/best-practices.md#active-learning)

## <a name="next-steps"></a>Passos seguintes

> [!div class="nextstepaction"]
> [Utilizar metadados com a API GenerateAnswer](metadata-generateanswer-usage.md)
