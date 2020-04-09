---
title: Analytics na base de conhecimentos - QnA Maker
titleSuffix: Azure Cognitive Services
description: A QnA Maker armazena todos os registos de chat e outras telemetrias, se tiver ativado a App Insights durante a criação do seu serviço QnA Maker. Faça as consultas de amostra para obter os seus registos de chat da App Insights.
services: cognitive-services
author: diberry
manager: nitinme
displayName: chat history, history, chat logs, logs
ms.service: cognitive-services
ms.subservice: qna-maker
ms.topic: conceptual
ms.date: 11/05/2019
ms.author: diberry
ms.openlocfilehash: d247c55112bc1c3cd921c0eda8e4ddadd6b5aed9
ms.sourcegitcommit: 2d7910337e66bbf4bd8ad47390c625f13551510b
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/08/2020
ms.locfileid: "80878073"
---
# <a name="get-analytics-on-your-knowledge-base"></a>Obter análises da base de dados de conhecimento

A QnA Maker armazena todos os registos de chat e outras telemetrias, se tiver ativado a App Insights durante a [criação do seu serviço QnA Maker](./set-up-qnamaker-service-azure.md). Faça as consultas de amostra para obter os seus registos de chat da App Insights.

1. Vá ao seu recurso App Insights.

    ![Selecione o seu recurso de insights de aplicação](../media/qnamaker-how-to-analytics-kb/resources-created.png)

2. Selecione **Registo (Analytics)**. Abre-se uma nova janela onde pode consultar a telemetria do QnA Maker.

3. Pasta na seguinte consulta e executá-la.

    ```kusto
    requests
    | where url endswith "generateAnswer"
    | project timestamp, id, url, resultCode, duration, performanceBucket
    | parse kind = regex url with *"(?i)knowledgebases/"KbId"/generateAnswer"
    | join kind= inner (
    traces | extend id = operation_ParentId
    ) on id
    | extend question = tostring(customDimensions['Question'])
    | extend answer = tostring(customDimensions['Answer'])
    | extend score = tostring(customDimensions['Score'])
    | project timestamp, resultCode, duration, id, question, answer, score, performanceBucket,KbId
    ```

    Selecione **Executar** para executar a consulta.

    [![Executar consulta para determinar perguntas, respostas e pontuação dos utilizadores](../media/qnamaker-how-to-analytics-kb/run-query.png)](../media/qnamaker-how-to-analytics-kb/run-query.png#lightbox)

## <a name="run-queries-for-other-analytics-on-your-qna-maker-knowledge-base"></a>Execute consultas para outras análises na sua base de conhecimento qnA Maker

### <a name="total-90-day-traffic"></a>Total de tráfego de 90 dias

```kusto
//Total Traffic
requests
| where url endswith "generateAnswer" and name startswith "POST"
| parse kind = regex url with *"(?i)knowledgebases/"KbId"/generateAnswer"
| summarize ChatCount=count() by bin(timestamp, 1d), KbId
```

### <a name="total-question-traffic-in-a-given-time-period"></a>Total de questiona tráfego em um determinado período de tempo

```kusto
//Total Question Traffic in a given time period
let startDate = todatetime('2019-01-01');
let endDate = todatetime('2020-12-31');
requests
| where timestamp <= endDate and timestamp >=startDate
| where url endswith "generateAnswer" and name startswith "POST"
| parse kind = regex url with *"(?i)knowledgebases/"KbId"/generateAnswer"
| summarize ChatCount=count() by KbId
```

### <a name="user-traffic"></a>Tráfego de utilizadores

```kusto
//User Traffic
requests
| where url endswith "generateAnswer"
| project timestamp, id, url, resultCode, duration
| parse kind = regex url with *"(?i)knowledgebases/"KbId"/generateAnswer"
| join kind= inner (
traces | extend id = operation_ParentId
) on id
| extend UserId = tostring(customDimensions['UserId'])
| summarize ChatCount=count() by bin(timestamp, 1d), UserId, KbId
```

### <a name="latency-distribution-of-questions"></a>Distribuição de perguntas por latência

```kusto
//Latency distribution of questions
requests
| where url endswith "generateAnswer" and name startswith "POST"
| parse kind = regex url with *"(?i)knowledgebases/"KbId"/generateAnswer"
| project timestamp, id, name, resultCode, performanceBucket, KbId
| summarize count() by performanceBucket, KbId
```

### <a name="unanswered-questions"></a>Perguntas sem resposta

```kusto
// Unanswered questions
requests
| where url endswith "generateAnswer"
| project timestamp, id, url
| parse kind = regex url with *"(?i)knowledgebases/"KbId"/generateAnswer"
| join kind= inner (
traces | extend id = operation_ParentId
) on id
| extend question = tostring(customDimensions['Question'])
| extend answer = tostring(customDimensions['Answer'])
| extend score = tostring(customDimensions['Score'])
| where  score  == "0" and message == "QnAMaker GenerateAnswer"
| project timestamp, KbId, question, answer, score
| order  by timestamp  desc
```

## <a name="next-steps"></a>Passos seguintes

> [!div class="nextstepaction"]
> [Escolha a capactiy](./improve-knowledge-base.md)
