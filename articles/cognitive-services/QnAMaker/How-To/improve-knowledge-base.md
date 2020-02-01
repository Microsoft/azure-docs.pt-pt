---
title: Melhorar a base de conhecimento - QnA Maker
titleSuffix: Azure Cognitive Services
description: Melhore a qualidade da sua base de conhecimento com aprendizagem ativa. Reveja, aceite ou rejeite, adicione sem remover ou alterar as questões existentes.
author: diberry
manager: nitinme
services: cognitive-services
ms.service: cognitive-services
ms.subservice: qna-maker
ms.topic: conceptual
ms.date: 01/28/2020
ms.author: diberry
ms.openlocfilehash: cadbf5fa88db7d5e524cb7e075745c03a844f750
ms.sourcegitcommit: 67e9f4cc16f2cc6d8de99239b56cb87f3e9bff41
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 01/31/2020
ms.locfileid: "76901705"
---
# <a name="use-active-learning-to-improve-your-knowledge-base"></a>Utilizar a aprendizagem ativa para melhorar a base de dados de conhecimento

A aprendizagem ativa permite-lhe melhorar a qualidade da sua base de conhecimentos sugerindo perguntas alternativas, baseadas em submissões de utilizador, ao seu par de perguntas e respostas. Revê essas sugestões, quer as adicione às perguntas existentes ou as rejeite.

A sua base de conhecimento não muda automaticamente. Para que qualquer alteração entre em vigor, deve aceitar as sugestões. Estas sugestões acrescentam perguntas, mas não alteram ou removem as questões existentes.

## <a name="what-is-active-learning"></a>O que é a aprendizagem ativa?

QnA Maker aprende novas variações de perguntas com feedback implícito e explícito.

* [Feedback implícito](#how-qna-makers-implicit-feedback-works) – O ranker compreende quando uma pergunta do utilizador tem múltiplas respostas com pontuações muito próximas e considera isso como feedback. Não precisas de fazer nada para que isto aconteça.
* [Feedback explícito](#how-you-give-explicit-feedback-with-the-train-api) – Quando várias respostas com pouca variação nas pontuações são devolvidas da base de conhecimento, a aplicação do cliente pergunta ao utilizador qual a pergunta correta. O feedback explícito do utilizador é enviado para o Fabricante qnA com a [API](#train-api)do comboio .

Ambos os métodos fornecem ao ranker consultas semelhantes que são agrupadas.

## <a name="how-active-learning-works"></a>Como funciona a aprendizagem ativa

A aprendizagem ativa é desencadeada com base nas pontuações das poucas respostas mais altas devolvidas pela QnA Maker. Se as diferenças de pontuação estiverem dentro de um pequeno intervalo, então a consulta é considerada uma possível sugestão (como uma pergunta alternativa) para cada um dos possíveis pares QnA. Uma vez que você aceita a pergunta sugerida para um par qnA específico, é rejeitado para os outros pares. Tens de te lembrar de poupar e treinar, depois de aceitarsugestões.

A aprendizagem ativa dá as melhores sugestões possíveis nos casos em que os pontos finais estão recebendo uma quantidade razoável e uma variedade de consultas de uso. Quando 5 ou mais consultas semelhantes são agrupadas, a cada 30 minutos, a QnA Maker sugere que as perguntas baseadas no utilizador ao designer de base de conhecimento aceitem ou rejeitem. Todas as sugestões são agrupadas por semelhanças e as sugestões de topo para perguntas alternativas são apresentadas com base na frequência das consultas específicas pelos utilizadores finais.

Uma vez sugeridas perguntas no portal QnA Maker, é necessário rever e aceitar ou rejeitar essas sugestões. Não há uma API para gerir sugestões.

## <a name="how-qna-makers-implicit-feedback-works"></a>Como funciona o feedback implícito do QnA Maker

O feedback implícito do QnA Maker usa um algoritmo para determinar a proximidade da pontuação e depois fazer sugestões de aprendizagem ativas. O algoritmo para determinar a proximidade não é um cálculo simples. As gamas no exemplo seguinte não devem ser corrigidas, mas devem ser usadas como guia para entender apenas o impacto do algoritmo.

Quando a pontuação de uma pergunta é altamente confiante, como 80%, o leque de pontuações que são consideradas para a aprendizagem ativa são amplas, aproximadamente dentro de 10%. À medida que a pontuação da confiança diminui, como 40%, o intervalo de pontuações também diminui, aproximadamente dentro de 4%.

## <a name="how-you-give-explicit-feedback-with-the-train-api"></a>Como dá feedback explícito com a API do comboio

É importante que a QnA Maker obtenha feedback explícito sobre qual das respostas foi a melhor resposta. Como a melhor resposta é determinada é consigo e pode incluir:

* Feedback do utilizador, selecionando uma das respostas.
* Lógica empresarial, como determinar um intervalo de pontuação aceitável.
* Uma combinação de feedback do utilizador e lógica de negócio.

## <a name="upgrade-your-runtime-version-to-use-active-learning"></a>Atualize a sua versão de tempo de execução para utilizar a aprendizagem ativa

Ative Learning é suportado na versão runtime 4.4.0 e acima. Se a sua base de conhecimento foi criada numa versão anterior, [atualize o seu tempo](set-up-qnamaker-service-azure.md#get-the-latest-runtime-updates) de execução para utilizar esta funcionalidade.

## <a name="turn-on-active-learning-to-see-suggestions"></a>Ligue a aprendizagem ativa para ver sugestões

A aprendizagem ativa está desligada por defeito. Ligue-o para ver as perguntas sugeridas. Depois de ativar a aprendizagem ativa, precisa enviar informações da app do cliente para o QnA Maker. Para obter mais informações, consulte o [fluxo arquitetónico para utilizar a GenerateAnswer e o Train APIs a partir de um bot](#architectural-flow-for-using-generateanswer-and-train-apis-from-a-bot).

1. Selecione **Publicar** para publicar a base de conhecimentos. As consultas de aprendizagem ativa são recolhidas apenas a partir do ponto final de previsão Da API GenerateAnswer. As consultas ao painel de teste no portal QnA Maker não têm impacto na aprendizagem ativa.

1. Para ativar a aprendizagem ativa no portal QnA Maker, vá para o canto superior direito, selecione o seu **Nome,** vá para [**definições**](https://www.qnamaker.ai/UserSettings)de serviço .

    ![Ligue as alternativas de perguntas sugeridas pela aprendizagem ativa a partir da página de definições do Serviço. Selecione o nome do utilizador no menu superior direito e, em seguida, selecione Definições de Serviço.](../media/improve-knowledge-base/Endpoint-Keys.png)


1. Encontre o serviço QnA Maker e, em seguida, alternar **a Aprendizagem Ativa**.

    > [!div class="mx-imgBorder"]
    > [![na página de definições do Serviço, alternar na funcionalidade Aprendizagem Ativa. Se não conseguir alternar a funcionalidade, poderá necessitar de atualizar o seu serviço.](../media/improve-knowledge-base/turn-active-learning-on-at-service-setting.png)](../media/improve-knowledge-base/turn-active-learning-on-at-service-setting.png#lightbox)

    > [!Note]
    > A versão exata na imagem anterior é mostrada apenas como um exemplo. A sua versão pode ser diferente.

    Uma vez ativada a **Ative Learning,** a base de conhecimentos sugere novas questões em intervalos regulares com base em questões submetidas pelo utilizador. Pode desativar a **Aprendizagem Ativa** toggling a configuração novamente.

## <a name="accept-an-active-learning-suggestion-in-the-knowledge-base"></a>Aceite uma sugestão de aprendizagem ativa na base de conhecimentos

A Ative Learning altera a Base de Conhecimento ou o Serviço de Pesquisa depois de aprovar a sugestão, em seguida, guardar e treinar. Se aprovar a sugestão, será adicionada como uma pergunta alternativa.

1. Para ver as perguntas sugeridas, na página base de conhecimento **editar,** selecione **Ver Opções,** em seguida, selecione Sugestões de **aprendizagem ativa do Show**.

    [![Na secção Editar do portal, selecione Sugestões de Espetáculos para ver as novas alternativas de pergunta da aprendizagem ativa.](../media/improve-knowledge-base/show-suggestions-button.png)](../media/improve-knowledge-base/show-suggestions-button.png#lightbox)

1. Filtre a base de conhecimento com pares de perguntas e respostas para mostrar apenas sugestões selecionando **Filtro por Sugestões**.

    [![Use o Filtro por sugestões para visualizar apenas as alternativas de pergunta sugeridas pela aprendizagem ativa.](../media/improve-knowledge-base/filter-by-suggestions.png)](../media/improve-knowledge-base/filter-by-suggestions.png#lightbox)

1. Cada par de QnA sugere que as novas alternativas de pergunta com uma marca de verificação, `✔`, para aceitar a pergunta ou uma `x` para rejeitar as sugestões. Selecione a marca de verificação para adicionar a pergunta.

    [![Selecione ou rejeite alternativas de perguntas sugeridas pela aprendizagem ativa selecionando a marca de verificação verde ou a marca de eliminação vermelha.](../media/improve-knowledge-base/accept-active-learning-suggestions.png)](../media/improve-knowledge-base/accept-active-learning-suggestions.png#lightbox)

    Pode adicionar ou eliminar _todas as sugestões_ selecionando **Adicionar tudo** ou rejeitar **tudo** na barra de ferramentas contextual.

1. Selecione **Guardar e Treinar** para guardar as alterações na base de conhecimentos.

1. **Selecione Publicar** para permitir que as alterações estejam disponíveis a partir do [API GenerateAnswer](metadata-generateanswer-usage.md#generateanswer-request-configuration).

    Quando 5 ou mais consultas semelhantes são agrupadas, a cada 30 minutos, a QnA Maker sugere as perguntas alternativas para que aceite ou rejeite.


<a name="#score-proximity-between-knowledge-base-questions"></a>

### <a name="architectural-flow-for-using-generateanswer-and-train-apis-from-a-bot"></a>Fluxo arquitetônico para usar GenerateAnswer e Treinar APIs de um bot

Um bot ou outra aplicação de cliente deve utilizar o seguinte fluxo arquitetónico para utilizar a aprendizagem ativa:

* Bot [obtém a resposta da base de conhecimento](#use-the-top-property-in-the-generateanswer-request-to-get-several-matching-answers) com a GenerateAnswer API, usando a propriedade `top` para obter uma série de respostas.
* Bot determina feedback explícito:
    * Utilizando a sua própria [lógica de negócio personalizada,](#use-the-score-property-along-with-business-logic-to-get-list-of-answers-to-show-user)filtre pontuações baixas.
    * No bot ou na aplicação do cliente, exiba a lista de possíveis respostas ao utilizador e obtenha a resposta selecionada do utilizador.
* Bot [envia resposta selecionada de volta para QnA Maker](#bot-framework-sample-code) com o Comboio [API](#train-api).


### <a name="use-the-top-property-in-the-generateanswer-request-to-get-several-matching-answers"></a>Use a propriedade superior no pedido GenerateAnswer para obter várias respostas correspondentes

Ao submeter uma pergunta ao QnA Maker para obter uma resposta, a propriedade `top` do corpo JSON define o número de respostas a devolver.

```json
{
    "question": "wi-fi",
    "isTest": false,
    "top": 3
}
```

### <a name="use-the-score-property-along-with-business-logic-to-get-list-of-answers-to-show-user"></a>Use a propriedade de pontuação juntamente com a lógica do negócio para obter a lista de respostas para mostrar o utilizador

Quando a aplicação do cliente (como um chat bot) recebe a resposta, as 3 principais questões são devolvidas. Use a propriedade `score` para analisar a proximidade entre pontuações. Esta gama de proximidade é determinada pela sua própria lógica de negócio.

```json
{
    "answers": [
        {
            "questions": [
                "Wi-Fi Direct Status Indicator"
            ],
            "answer": "**Wi-Fi Direct Status Indicator**\n\nStatus bar icons indicate your current Wi-Fi Direct connection status:  \n\nWhen your device is connected to another device using Wi-Fi Direct, '$  \n\n+ •+ ' Wi-Fi Direct is displayed in the Status bar.",
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

A aplicação do seu cliente apresenta as perguntas com opção para que o utilizador selecione _a única questão_ que mais representa a sua intenção.

Uma vez que o utilizador selecione uma das questões existentes, a aplicação do cliente envia a escolha do utilizador como feedback usando a API de comboio da QnA Maker. Este feedback completa o ciclo de feedback de aprendizagem ativa.

## <a name="train-api"></a>API de Preparação

O feedback de aprendizagem ativa é enviado para o Fabricante qnA com o pedido do Train API POST. A assinatura DaPi é:

```http
POST https://<QnA-Maker-resource-name>.azurewebsites.net/qnamaker/knowledgebases/<knowledge-base-ID>/train
Authorization: EndpointKey <endpoint-key>
Content-Type: application/json
{"feedbackRecords": [{"userId": "1","userQuestion": "<question-text>","qnaId": 1}]}
```

|HTTP solicitar propriedade|Nome|Tipo|Finalidade|
|--|--|--|--|
|Parâmetro de rota URL|ID da base de dados de conhecimento|string|O GUID para a sua base de conhecimento.|
|Subdomínio personalizado|Nome de recurso QnAMaker|string|O nome do recurso é usado como subdomínio personalizado para o seu Fabricante QnA. Isto está disponível na página Definições depois de publicar a base de conhecimentos. Está listado como o `host`.|
|Cabeçalho|Tipo de conteúdo|string|O tipo de mídia do corpo enviado para a API. O valor predefinido é: `application/json`|
|Cabeçalho|Autorização|string|A sua chave de ponto final (EndpointKey xxxxxx-xxxx-xxxx-xxxx-xxxxxxxxx).|
|Corpo da postagem|Objeto JSON|JSON|O feedback de treino|

O corpo jSON tem várias configurações:

|Propriedade corporal JSON|Tipo|Finalidade|
|--|--|--|--|
|`feedbackRecords`|array|Lista de comentários.|
|`userId`|string|A identificação do utilizador da pessoa que aceita as perguntas sugeridas. O formato ID do utilizador depende de si. Por exemplo, um endereço de e-mail pode ser um ID de utilizador válido na sua arquitetura. Opcional.|
|`userQuestion`|string|Texto exato da consulta do utilizador. Necessário.|
|`qnaID`|número|Id de pergunta, encontrado na [resposta GenerateAnswer](metadata-generateanswer-usage.md#generateanswer-response-properties). |

Um exemplo de corpo JSON é semelhante a:

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

### <a name="batch-many-feedback-records-into-a-single-call"></a>Emlota muitos registos de feedback numa única chamada

Na aplicação do lado do cliente, como um bot, pode armazenar os dados e, em seguida, enviar muitos registos num único corpo JSON na matriz de `feedbackRecords`.

Um exemplo de corpo JSON é semelhante a:

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

## <a name="bot-framework-sample-code"></a>Código de amostra de quadro bot

O seu código-quadro bot precisa de chamar a API do comboio, caso a consulta do utilizador seja utilizada para uma aprendizagem ativa. Há duas peças de código para escrever:

* Determine se a consulta deve ser usada para a aprendizagem ativa
* Envie consulta de volta à QnA Maker's Train API para aprendizagem ativa

Na [amostra Azure Bot,](https://aka.ms/activelearningsamplebot)ambas as atividades foram programadas.

### <a name="example-c-code-for-train-api-with-bot-framework-4x"></a>Código C# de exemplo para Trem API com Bot Framework 4.x

O código que se segue ilustra como enviar informações de volta para o Fabricante QnA com a API do comboio. Esta [amostra completa de código](https://github.com/microsoft/BotBuilder-Samples/tree/master/experimental/qnamaker-activelearning/csharp_dotnetcore) está disponível no GitHub.

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

### <a name="example-nodejs-code-for-train-api-with-bot-framework-4x"></a>Exemplo Node.js código para Trem API com Bot Framework 4.x

O código que se segue ilustra como enviar informações de volta para o Fabricante QnA com a API do comboio. Esta [amostra completa de código](https://github.com/microsoft/BotBuilder-Samples/blob/master/experimental/qnamaker-activelearning/javascript_nodejs) está disponível no GitHub.

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

## <a name="active-learning-is-saved-in-the-exported-knowledge-base"></a>A aprendizagem ativa é salva na base de conhecimentos exportados

Quando a sua aplicação tem uma aprendizagem ativa ativa ativa, e exporta a app, a coluna `SuggestedQuestions` no ficheiro TSV retém os dados de aprendizagem ativa.

A coluna `SuggestedQuestions` é um objeto jSON de informação de feedback implícito, `autosuggested`e explícito, `usersuggested`. Um exemplo deste objeto JSON para uma única questão de `help` apresentada pelo utilizador é:

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

Também pode utilizar as alterações de descarregamento API para rever estas alterações, utilizando REST ou qualquer um dos SDKs baseados em idiomas:
* [API REST](https://westus.dev.cognitive.microsoft.com/docs/services/5a93fcf85b4ccd136866eb37/operations/5ac266295b4ccd1554da75fc)
* [SDK do .NET](https://docs.microsoft.com/dotnet/api/microsoft.azure.cognitiveservices.knowledge.qnamaker.alterationsextensions.getasync?view=azure-dotnet)


Ao reimportar esta aplicação, a aprendizagem ativa continua a recolher informações e a recomendar sugestões para a sua base de conhecimentos.



## <a name="best-practices"></a>Melhores práticas

Para obter as melhores práticas ao utilizar a aprendizagem ativa, consulte [as melhores práticas.](../Concepts/best-practices.md#active-learning)

## <a name="next-steps"></a>Passos seguintes

> [!div class="nextstepaction"]
> [Utilize metadados com API GenerateAnswer](metadata-generateanswer-usage.md)
