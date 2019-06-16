---
title: Melhorar a base de dados de conhecimento - QnA Maker
titleSuffix: Azure Cognitive Services
description: Aprendizagem ativa permite-lhe melhorar a qualidade da sua base de dados de conhecimento ao sugerir perguntas alternativas, com base no utilizador envios, para o par de perguntas e respostas. Examinar essas sugestões, a adicioná-las à existente perguntas ou rejeitá-los. A base de dados de conhecimento não é alterado automaticamente. Tem de aceitar as sugestões para todas as alterações entrem em vigor. Estas sugestões adicionar perguntas, mas não alterar ou remover perguntas existentes.
author: diberry
manager: nitinme
services: cognitive-services
ms.service: cognitive-services
ms.subservice: qna-maker
ms.topic: article
ms.date: 06/06/2019
ms.author: diberry
ms.openlocfilehash: f8d2f6d9fce6a249a782f959ac7672ac8e123fbc
ms.sourcegitcommit: 41ca82b5f95d2e07b0c7f9025b912daf0ab21909
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 06/13/2019
ms.locfileid: "67075165"
---
# <a name="use-active-learning-to-improve-your-knowledge-base"></a>Utilizar o Active Directory de aprendizagem para melhorar a sua base de dados de conhecimento

Aprendizagem ativa permite-lhe melhorar a qualidade da sua base de dados de conhecimento ao sugerir perguntas alternativas, com base no utilizador envios, para o par de perguntas e respostas. Examinar essas sugestões, a adicioná-las à existente perguntas ou rejeitá-los. 

A base de dados de conhecimento não é alterado automaticamente. Para todas as alterações entrem em vigor, tem de aceitar as sugestões. Estas sugestões adicionar perguntas, mas não alterar ou remover perguntas existentes.

## <a name="what-is-active-learning"></a>O que é a aprendizagem ativa?

A ferramenta QnA Maker aprende novas variações de pergunta com comentários implícita e explícita.
 
* [Comentários implícito](#how-qna-makers-implicit-feedback-works) – o classificador compreende quando uma pergunta de utilizador tem várias respostas com classificações que são muito parecidas e considera-o como comentários. Não precisa de fazer nada para que isso aconteça.
* [Comentários explícitos](#how-you-give-explicit-feedback-with-the-train-api) -se várias respostas com pouca variação na pontuações são devolvidas a partir da base de dados de conhecimento, o aplicativo cliente pede ao usuário que pergunta é a pergunta correta. Comentários de explícita do utilizador são enviados para o QnA Maker com o [Train API](#train-api). 

Ambos os métodos fornecem o classificador com consultas similares estão agrupados em cluster.

## <a name="how-active-learning-works"></a>Como Active Directory funciona de aprendizagem

Aprendizagem ativa é acionada com base em classificações de respostas alguns principais devolvidas pelo QnA Maker. Se as diferenças de pontuação se situam dentro de um pequeno intervalo, em seguida, a consulta é considerada uma sugestão possíveis (como uma pergunta alternativa) para cada um dos pares de QnA possíveis. Depois de aceitar a pergunta sugerida para um par de QnA específica, é rejeitada para os pares de outros. Precisa se lembrar guardar e dar formação e depois de aceitar as sugestões.

Aprendizagem ativa fornece as sugestões de possíveis melhor em casos em que os pontos finais estão a obter uma quantidade razoável e a variedade de consultas de utilização. Quando as consultas de 5 ou mais semelhantes são colocados num cluster, cada 30 minutos, a ferramenta QnA Maker sugere perguntas baseada no utilizador para o estruturador de base de dados de conhecimento para aceitar ou rejeitar. Todas as sugestões sejam agrupadas por semelhança e sugestões principais para perguntas alternativas são apresentadas com base na frequência das consultas determinadas pelos usuários finais.

Assim que as perguntas são sugeridas no portal do QnA Maker, terá de rever e aceitar ou rejeitar essas sugestões. Não existem é uma API para gerir sugestões.

## <a name="how-qna-makers-implicit-feedback-works"></a>Como funciona a comentários implícito do QnA Maker

Comentários implícito do QnA Maker usa um algoritmo para determinar a proximidade de pontuação, em seguida, fazer sugestões de aprendizagem ativa. O algoritmo para determinar a proximidade não é um cálculo simple. Os intervalos no exemplo a seguir não devem ser corrigidos, mas devem ser utilizados como um guia para compreender o impacto do algoritmo apenas.

Quando a pontuação de uma pergunta é altamente seguro, por exemplo, 80%, o intervalo de classificações que são considerados para a aprendizagem ativa são ampla, aproximadamente dentro de 10%. À medida que diminui a pontuação de confiança, como a 40%, o intervalo das pontuações diminui, aproximadamente dentro de 4%. 

## <a name="how-you-give-explicit-feedback-with-the-train-api"></a>Como enviar comentários explícita com a API de formação

É importante que o QnA Maker obtém comentários explícito que sobre quais as respostas foi a melhor resposta. Como é determinada a melhor resposta cabe a e pode incluir:

* Comentários dos utilizadores, selecionar uma das respostas.
* Lógica de negócios, como ao determinar um aceitável intervalo de pontuação.  
* Uma combinação de ambos os usuário comentários e lógica de negócio.

## <a name="upgrade-your-runtime-version-to-use-active-learning"></a>Atualizar a sua versão de runtime para utilizar a aprendizagem ativa

Aprendizagem ativa é suportada na versão de runtime 4.4.0 e acima. Se a sua base de dados de conhecimento foi criado numa versão anterior, [atualizar o runtime](troubleshooting-runtime.md#how-to-get-latest-qnamaker-runtime-updates) para utilizar esta funcionalidade. 

## <a name="turn-on-active-learning-to-see-suggestions"></a>Ativar o Active Directory de aprendizagem para ver sugestões

Aprendizagem ativa está desativada por predefinição. A ativá-lo para ver perguntas sugeridas. Depois de ativar a aprendizagem ativa, terá de enviar informações da aplicação de cliente para a ferramenta QnA Maker. Para obter mais informações, consulte [fluxo de arquitetura para utilizar GenerateAnswer e as APIs de comboio de um bot](#architectural-flow-for-using-generateanswer-and-train-apis-from-a-bot).

1. Selecione **publicar** para publicar a base de dados de conhecimento. Consultas de aprendizagem ativa são recolhidas a partir do GenerateAnswer predição ponto final de API apenas. As consultas para o painel de teste no portal do QnA Maker não afetam aprendizagem ativa.

1. Para ativar o Active Directory de aprendizado no portal do QnA Maker, vá para o canto superior direito, selecione seu **Name**, aceda a [ **definições de serviço**](https://www.qnamaker.ai/UserSettings).  

    ![Ative a aprendizagem ativa sugeridas alternativas de pergunta da página de definições de serviço. Selecione o seu nome de utilizador no menu superior direito, em seguida, selecione as definições do serviço.](../media/improve-knowledge-base/Endpoint-Keys.png)


1. Encontrar o serviço QnA Maker, em seguida, alternar **aprendizagem ativa**. 

    [![Na página de definições de serviço, ative a funcionalidade de aprendizagem ativa. Se não conseguir Ativar/desativar a funcionalidade, terá de atualizar o seu serviço.](../media/improve-knowledge-base/turn-active-learning-on-at-service-setting.png)](../media/improve-knowledge-base/turn-active-learning-on-at-service-setting.png#lightbox)

    Uma vez **aprendizagem ativa** é ativada, a base de dados de conhecimento sugere novas questões em intervalos regulares, com base nas perguntas enviadas por utilizador. Pode desabilitar **aprendizagem ativa** , Ativando a definição novamente.

## <a name="accept-an-active-learning-suggestion-in-the-knowledge-base"></a>Aceitar uma sugestão de aprendizagem ativa na base de dados de conhecimento

1. Para ver perguntas sugeridas, no **edite** página base de dados de conhecimento, selecione **opções de visualização**, em seguida, selecione **Mostrar sugestões de aprendizagem ativa**. 

    [![Na secção de edição do portal, selecione Mostrar sugestões para ver novas alternativas de pergunta a aprendizagem ativa.](../media/improve-knowledge-base/show-suggestions-button.png)](../media/improve-knowledge-base/show-suggestions-button.png#lightbox)

1. Filtrar a base de dados de conhecimento com pares de perguntas e respostas para mostrar apenas sugestões selecionando **filtrar por sugestões**.

    [![Utilize o filtro, utilize sugestões para ver as alternativas de pergunta sugeridos apenas a aprendizagem ativa.](../media/improve-knowledge-base/filter-by-suggestions.png)](../media/improve-knowledge-base/filter-by-suggestions.png#lightbox)

1. Cada par de QnA sugere as alternativas de pergunta nova com uma marca de verificação `✔` , para aceitar a pergunta ou um `x` para rejeitar as sugestões. Selecione a marca de verificação para adicionar a pergunta. 

    [![Selecione ou rejeitar alternativas de pergunta sugerido de aprendizagem ativa, selecionando a marca de verificação verde ou a marca de exclusão vermelho.](../media/improve-knowledge-base/accept-active-learning-suggestions.png)](../media/improve-knowledge-base/accept-active-learning-suggestions.png#lightbox)

    Pode adicionar ou eliminar _todas as sugestões_ ao selecionar **adicione todos** ou **rejeitar tudo** na barra de ferramentas contextual.

1. Selecione **guardar e treinar** para guardar as alterações para a base de dados de conhecimento.

1. Selecione **Publish** para permitir que as alterações estar disponível a partir do [GenerateAnswer API](metadata-generateanswer-usage.md#generateanswer-request-configuration).

    Quando as consultas de 5 ou mais semelhantes são colocados num cluster, cada 30 minutos, a ferramenta QnA Maker sugere perguntas alternativas para que possa aceitar ou rejeitar.


<a name="#score-proximity-between-knowledge-base-questions"></a>

### <a name="architectural-flow-for-using-generateanswer-and-train-apis-from-a-bot"></a>Fluxo de arquitetura para utilizar GenerateAnswer e as APIs de comboio de um bot

Um bot ou outra aplicação de cliente deve utilizar o seguinte fluxo de arquitetura para utilizar a aprendizagem ativa:

* Bot [obtém a resposta a partir da base de dados de conhecimento](#use-the-top-property-in-the-generateanswer-request-to-get-several-matching-answers) com a API de GenerateAnswer, usando o `top` propriedade para obter um número de respostas.
* Bot determina comentários explícito:
    * Utilizar o seu próprio [lógica de negócio personalizada](#use-the-score-property-along-with-business-logic-to-get-list-of-answers-to-show-user), filtrar as pontuações de baixas.
    * O bot ou aplicação de cliente, exibir a lista de possíveis respostas para o usuário e obter resposta selecionada do usuário.
* Bot [envia a resposta selecionada para a ferramenta QnA Maker](#bot-framework-sample-code) com o [Train API](#train-api).


### <a name="use-the-top-property-in-the-generateanswer-request-to-get-several-matching-answers"></a>Utilize a propriedade superior no pedido GenerateAnswer para obter várias respostas correspondentes

Ao submeter uma pergunta para a ferramenta QnA Maker para uma resposta, o `top` propriedade do corpo JSON define o número de respostas a devolver. 

```json
{
    "question": "wi-fi",
    "isTest": false,
    "top": 3
}
```

### <a name="use-the-score-property-along-with-business-logic-to-get-list-of-answers-to-show-user"></a>Utilize a propriedade de pontuação, juntamente com a lógica de negócios para obter a lista de respostas para mostrar o utilizador

Quando a aplicação de cliente (como um chatbot) recebe a resposta, são devolvidas melhores 3 perguntas. Utilize o `score` propriedade para analisar a proximidade entre as pontuações. Este intervalo de proximidade é determinado pela sua própria lógica de negócios. 

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

## <a name="client-application-follow-up-when-questions-have-similar-scores"></a>Acompanhamento de aplicação de cliente quando perguntas têm pontuações semelhantes

A aplicação cliente apresenta as perguntas com a opção para o usuário selecione _a pergunta única_ que mais representa sua intenção. 

Assim que o usuário seleciona uma das perguntas existentes, a aplicação cliente envia a escolha do usuário como comentários utilizando a API do QnA Maker Train. Estes comentários conclui o Active Directory ciclo de comentários de aprendizado. 

## <a name="train-api"></a>API de formação

Comentários de aprendizagem ativa será enviado para a ferramenta QnA Maker com o pedido POST de API de formação. A assinatura de API é:

```http
POST https://<QnA-Maker-resource-name>.azurewebsites.net/qnamaker/knowledgebases/<knowledge-base-ID>/train
Authorization: EndpointKey <endpoint-key>
Content-Type: application/json
{"feedbackRecords": [{"userId": "1","userQuestion": "<question-text>","qnaId": 1}]}
```

|Propriedade de pedido HTTP|Name|Tipo|Objetivo|
|--|--|--|--|
|Parâmetro de rota de URL|ID da base de dados de conhecimento|string|O GUID de sua base de dados de conhecimento.|
|Host subdomain|Nome do recurso QnAMaker|string|O nome de anfitrião para o QnA Maker na sua subscrição do Azure. Está disponível na página definições depois de publicar a base de dados de conhecimento. |
|Cabeçalho|Content-Type|string|O tipo de suporte do corpo enviado para a API. Valor predefinido é: `application/json`|
|Cabeçalho|Autorização|string|A chave de ponto final (EndpointKey xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx).|
|Corpo da mensagem|Objeto JSON|JSON|Os comentários de treinamento|

O corpo JSON tem várias definições:

|Propriedade de corpo JSON|Tipo|Objetivo|
|--|--|--|--|
|`feedbackRecords`|array|Lista de comentários.|
|`userId`|string|O ID de utilizador da pessoa aceitar as perguntas sugeridas. O formato do ID de utilizador cabe a. Por exemplo, um endereço de e-mail pode ser um ID de utilizador válido na sua arquitetura. Opcional.|
|`userQuestion`|string|Texto exato da pergunta. Necessário.|
|`qnaID`|número|ID de pergunta, encontrada no [GenerateAnswer resposta](metadata-generateanswer-usage.md#generateanswer-response-properties). |

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

Uma resposta com êxito retorna um status de 204 e nenhum corpo de resposta JSON. 

<a name="active-learning-is-saved-in-the-exported-apps-tsv-file"></a>

## <a name="bot-framework-sample-code"></a>Código de exemplo do bot framework

Código do bot framework precisa chamar a API de comboio, se a consulta do utilizador deve ser utilizada para a aprendizagem ativa. Existem duas partes de código para escrever:

* Determinar se a consulta deve ser utilizada para a aprendizagem ativa
* Enviar consulta para o QnA Maker Train API para aprendizagem ativa

Na [exemplo de Azure Bot](https://aka.ms/activelearningsamplebot), ambas as atividades tenham sido programadas. 

### <a name="example-c-code-for-train-api-with-bot-framework-4x"></a>Exemplo C# código para a API de Train com o Bot Framework 4.x

O código a seguir ilustra como enviar informações para o QnA Maker com a API de comboio. Isso [código de exemplo completo](https://github.com/microsoft/BotBuilder-Samples/tree/master/experimental/qnamaker-activelearning/csharp_dotnetcore) está disponível no GitHub.

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
/// <param name="host">Endpoint host of the runtime</param>
/// <param name="FeedbackRecords">Feedback records train API</param>
/// <param name="kbId">Knowledgebase Id</param>
/// <param name="key">Endpoint key</param>
/// <param name="cancellationToken"> Cancellation token</param>
public async static void CallTrain(string host, FeedbackRecords feedbackRecords, string kbId, string key, CancellationToken cancellationToken)
{
    var uri = host + "/knowledgebases/" + kbId + "/train/";

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

### <a name="example-nodejs-code-for-train-api-with-bot-framework-4x"></a>Código de node. js de exemplo para a API de Train com o Bot Framework 4.x 

O código a seguir ilustra como enviar informações para o QnA Maker com a API de comboio. Isso [código de exemplo completo](https://github.com/microsoft/BotBuilder-Samples/blob/master/experimental/qnamaker-activelearning/javascript_nodejs) está disponível no GitHub.

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

## <a name="active-learning-is-saved-in-the-exported-knowledge-base"></a>Aprendizagem ativa é guardada na base de dados de conhecimento exportada

Quando a aplicação tem a aprendizagem ativa ativada e exportar a aplicação, o `SuggestedQuestions` coluna no ficheiro tsv retém os dados de aprendizagem ativa. 

O `SuggestedQuestions` coluna é um objeto JSON de informações de implícito, o `autosuggested`e explícito, `usersuggested` comentários. Um exemplo deste objeto JSON para uma utilizador submeteu pergunta de `help` é:

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

Quando importe novamente esta aplicação, a aprendizagem ativa continua a recolher informações e recomendar sugestões para a sua base de dados de conhecimento. 

## <a name="best-practices"></a>Melhores práticas

Para melhores práticas quando utilizar a aprendizagem ativa, consulte [melhores práticas](../Concepts/best-practices.md#active-learning).

## <a name="next-steps"></a>Passos Seguintes
 
> [!div class="nextstepaction"]
> [Utilizar metadados com a API de GenerateAnswer](metadata-generateanswer-usage.md)
