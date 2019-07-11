---
title: Metadados com GenerateAnswer API - QnA Maker
titleSuffix: Azure Cognitive Services
description: A ferramenta QnA Maker permite-lhe adicionar metadados, na forma de pares chave/valor, a seus conjuntos de pergunta/resposta. Pode filtrar os resultados de consultas do utilizador e armazenar informações adicionais que podem ser utilizadas em conversas de acompanhamento.
services: cognitive-services
author: diberry
manager: nitinme
ms.service: cognitive-services
ms.subservice: qna-maker
ms.topic: article
ms.date: 06/27/2019
ms.author: diberry
ms.openlocfilehash: 6bfcb531d0e4e8073a5553f7bc84a25e4f8a92a9
ms.sourcegitcommit: 1572b615c8f863be4986c23ea2ff7642b02bc605
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 07/10/2019
ms.locfileid: "67785686"
---
# <a name="get-an-answer-with-the-generateanswer-api-and-metadata"></a>Obter uma resposta com a API de GenerateAnswer e metadados

Para obter a resposta prevista à pergunta de um utilizador, utilize a API de GenerateAnswer. Quando publica uma base de dados de conhecimento, pode ver informações sobre como utilizar esta API em como o **publicar** página. Também pode configurar a API para filtrar com base em etiquetas de metadados de respostas e testar a base de dados de conhecimento a partir do ponto final com o parâmetro de cadeia de caracteres de consulta de teste.

A ferramenta QnA Maker permite-lhe adicionar metadados, na forma de pares de chave e valor, a seus conjuntos de perguntas e respostas. Em seguida, pode utilizar estas informações para filtrar os resultados de consultas do utilizador e para armazenar informações adicionais que podem ser utilizadas no seguimento conversas. Para obter mais informações, consulte [base de dados de conhecimento](../Concepts/knowledge-base.md).

<a name="qna-entity"></a>

## <a name="store-questions-and-answers-with-a-qna-entity"></a>Store perguntas e respostas com uma entidade de QnA

É importante compreender como o QnA Maker armazena os dados de perguntas e respostas. A ilustração seguinte mostra uma entidade de QnA:

![Ilustração de uma entidade de QnA](../media/qnamaker-how-to-metadata-usage/qna-entity.png)

Cada entidade do QnA tem um ID exclusivo e persistente. Pode utilizar o ID para disponibilizar as atualizações para uma entidade específica do QnA.

<a name="generateanswer-api"></a>

## <a name="get-answer-predictions-with-the-generateanswer-api"></a>Obter previsões de resposta com a API de GenerateAnswer

Utilizar o [GenerateAnswer API](https://docs.microsoft.com/rest/api/cognitiveservices/qnamakerruntime/runtime/generateanswer) no seu bot ou aplicação para consultar a base de dados de conhecimento com uma pergunta de utilizador obter a melhor correspondência da pergunta e resposta define.

<a name="generateanswer-endpoint"></a>

## <a name="publish-to-get-generateanswer-endpoint"></a>Publicar para obter o ponto final de GenerateAnswer 

Depois de publicar a sua base de dados de conhecimento, a partir do [portal do QnA Maker](https://www.qnamaker.ai), ou utilizando o [API](https://docs.microsoft.com/rest/api/cognitiveservices/qnamaker/knowledgebase/publish), pode obter os detalhes do ponto final do GenerateAnswer.

Para obter os detalhes do seu ponto final:
1. Inicie sessão em [https://www.qnamaker.ai](https://www.qnamaker.ai).
1. Na **meu bases de dados de conhecimento**, selecione **View Code** para sua base de dados de conhecimento.
    ![Captura de ecrã da minha bases de dados de conhecimento](../media/qnamaker-how-to-metadata-usage/my-knowledge-bases.png)
1. Obtenha os detalhes do seu ponto final de GenerateAnswer.

    ![Captura de ecrã de detalhes do ponto final](../media/qnamaker-how-to-metadata-usage/view-code.png)

Também pode obter os detalhes do seu ponto final da **definições** separador da sua base de dados de conhecimento.

<a name="generateanswer-request"></a>

## <a name="generateanswer-request-configuration"></a>Configuração de pedido de GenerateAnswer

Chamar GenerateAnswer com um pedido HTTP POST. Para o código de exemplo que mostra como chamar GenerateAnswer, consulte a [inícios Rápidos](../quickstarts/csharp.md). 

O pedido POST utiliza:

* Necessário [parâmetros do URI](https://docs.microsoft.com/rest/api/cognitiveservices/qnamakerruntime/runtime/train#uri-parameters)
* Necessário [propriedade header](https://docs.microsoft.com/azure/cognitive-services/qnamaker/quickstarts/get-answer-from-knowledge-base-nodejs#add-a-post-request-to-send-question-and-get-an-answer), `Authorization`, para segurança
* Necessário [body propriedades](https://docs.microsoft.com/rest/api/cognitiveservices/qnamakerruntime/runtime/train#feedbackrecorddto). 

O URL de GenerateAnswer tem o seguinte formato: 

```
https://{QnA-Maker-endpoint}/knowledgebases/{knowledge-base-ID}/generateAnswer
```

Não se esqueça de definir a propriedade de cabeçalho HTTP do `Authorization` com um valor da cadeia de caracteres `EndpointKey` com à direita do espaço, em seguida, a chave de ponto de extremidade encontrada no **definições** página.

Um exemplo de corpo JSON é semelhante a:

```json
{
    "question": "qna maker and luis",
    "top": 6,
    "isTest": true,
    "scoreThreshold": 20,
    "strictFilters": [
    {
        "name": "category",
        "value": "api"
    }],
    "userId": "sd53lsY="
}
```

<a name="generateanswer-response"></a>

## <a name="generateanswer-response-properties"></a>Propriedades de resposta de GenerateAnswer

O [resposta](https://docs.microsoft.com/rest/api/cognitiveservices/qnamakerruntime/runtime/generateanswer#successful-query) é um objeto JSON, incluindo todas as informações necessárias apresentar a resposta e a próxima ativar na conversação, se disponível.

```json
{
    "answers": [
        {
            "score": 28.54820341616869,
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

## <a name="use-qna-maker-with-a-bot-in-c"></a>Utilize o QnA Maker com um bot emC#

O bot framework fornece acesso às propriedades do QnA Maker:

```csharp
using Microsoft.Bot.Builder.AI.QnA;
var metadata = new Microsoft.Bot.Builder.AI.QnA.Metadata();
var qnaOptions = new QnAMakerOptions();

qnaOptions.Top = Constants.DefaultTop;
qnaOptions.ScoreThreshold = 0.3F;
var response = await _services.QnAServices[QnAMakerKey].GetAnswersAsync(turnContext, qnaOptions);
```

O bot de suporte tiver [um exemplo](https://github.com/microsoft/BotBuilder-Samples/blob/master/experimental/qnamaker-support/csharp_dotnetcore/Service/SupportBotService.cs#L418) com esse código.

## <a name="use-qna-maker-with-a-bot-in-nodejs"></a>Utilize o QnA Maker com um bot em node. js

O bot framework fornece acesso às propriedades do QnA Maker:

```javascript
const { QnAMaker } = require('botbuilder-ai');
this.qnaMaker = new QnAMaker(endpoint);

// Default QnAMakerOptions
var qnaMakerOptions = {
    ScoreThreshold: 0.03,
    Top: 3
};
var qnaResults = await this.qnaMaker.getAnswers(stepContext.context, qnaMakerOptions);
```

O bot de suporte tiver [um exemplo](https://github.com/microsoft/BotBuilder-Samples/blob/master/experimental/qnamaker-activelearning/javascript_nodejs/Helpers/dialogHelper.js#L36) com esse código.

<a name="metadata-example"></a>

## <a name="use-metadata-to-filter-answers-by-custom-metadata-tags"></a>Usar metadados para filtrar respostas por etiquetas de metadados personalizados

Adicionar metadados permite-lhe filtrar as respostas por estas etiquetas de metadados. Adicionar a coluna de metadados a partir da **opções de visualização** menu. Adicionar metadados a sua base de dados de conhecimento, selecionando os metadados **+** ícone para adicionar um par de metadados. Esse par consiste numa chave e um valor.

![Captura de ecrã da adição de metadados](../media/qnamaker-how-to-metadata-usage/add-metadata.png)

<a name="filter-results-with-strictfilters-for-metadata-tags"></a>

## <a name="filter-results-with-strictfilters-for-metadata-tags"></a>Filtrar os resultados com strictFilters para marcas de metadados

Considere a pergunta do utilizador "Quando o feche esta hotel?", em que a intenção é implícito para o restaurante "Paraíso."

Uma vez que os resultados são necessários apenas para o restaurante "Paraíso", pode definir um filtro na chamada GenerateAnswer nos metadados "Restaurante Name". O exemplo seguinte mostra esta:

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

## <a name="use-question-and-answer-results-to-keep-conversation-context"></a>Utilize os resultados de perguntas e respostas para manter o contexto de conversação

A resposta para o GenerateAnswer contém as informações de metadados correspondente do conjunto de perguntas e respostas correspondente. Pode usar essas informações na sua aplicação de cliente para armazenar o contexto da conversa anterior para utilização posterior conversas. 

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

## <a name="match-questions-only-by-text"></a>Corresponder só, perguntas pelo texto

Por predefinição, o QnA Maker procura por meio de perguntas e respostas. Se quiser pesquisar apenas perguntas, para gerar uma resposta, utilize o `RankerType=QuestionOnly` no corpo POST da solicitação GenerateAnswer.

Pode pesquisar por meio da kb publicado, usando `isTest=false`, ou no teste kb com `isTest=true`.

```json
{
  "question": "Hi",
  "top": 30,
  "isTest": true,
  "RankerType":"QuestionOnly"
}
```

## <a name="next-steps"></a>Passos Seguintes

O **Publish** página também fornece informações para gerar uma resposta com [Postman](../Quickstarts/get-answer-from-kb-using-postman.md) e [cURL](../Quickstarts/get-answer-from-kb-using-curl.md). 

> [!div class="nextstepaction"]
> [Criar uma base de dados de conhecimento](./create-knowledge-base.md)
