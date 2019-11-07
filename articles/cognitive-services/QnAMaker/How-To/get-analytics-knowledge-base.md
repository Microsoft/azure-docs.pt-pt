---
title: Análise na base de conhecimentos-QnA Maker
titleSuffix: Azure Cognitive Services
description: QnA Maker armazenará todos os logs de chat e outras telemetrias, se você tiver habilitado o Application insights durante a criação de seu serviço de QnA Maker. Execute as consultas de exemplo para fazer seus logs de chat do App insights.
services: cognitive-services
author: diberry
manager: nitinme
displayName: chat history, history, chat logs, logs
ms.service: cognitive-services
ms.subservice: qna-maker
ms.topic: conceptual
ms.date: 11/05/2019
ms.author: diberry
ms.openlocfilehash: 72d2598c1ff17f80fc264e6d547a799ab74a163f
ms.sourcegitcommit: 6c2c97445f5d44c5b5974a5beb51a8733b0c2be7
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 11/05/2019
ms.locfileid: "73621965"
---
# <a name="get-analytics-on-your-knowledge-base"></a>Obter análises da base de dados de conhecimento

QnA Maker armazenará todos os logs de chat e outras telemetrias, se você tiver habilitado o Application insights durante a [criação de seu serviço de QnA Maker](./set-up-qnamaker-service-azure.md). Execute as consultas de exemplo para fazer seus logs de chat do App insights.

1. Vá para o recurso do App insights.

    ![Selecione o recurso do Application insights](../media/qnamaker-how-to-analytics-kb/resources-created.png)

2. Selecione **log (análise)** . Uma nova janela é aberta, onde você pode consultar QnA Maker telemetria.

3. Cole a consulta a seguir e execute-a.

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

    Selecione **executar** para executar a consulta.

    [![executar a consulta para determinar as perguntas, as respostas e a Pontuação dos usuários](../media/qnamaker-how-to-analytics-kb/run-query.png)](../media/qnamaker-how-to-analytics-kb/run-query.png#lightbox)

## <a name="run-queries-for-other-analytics-on-your-qna-maker-knowledge-base"></a>Executar consultas para outras análises em sua base de dados de conhecimento QnA Maker

### <a name="total-90-day-traffic"></a>Total de tráfego de 90 dias

```kusto
//Total Traffic
requests
| where url endswith "generateAnswer" and name startswith "POST"
| parse kind = regex url with *"(?i)knowledgebases/"KbId"/generateAnswer" 
| summarize ChatCount=count() by bin(timestamp, 1d), KbId
```

### <a name="total-question-traffic-in-a-given-time-period"></a>Tráfego de pergunta total em um determinado período de tempo

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

### <a name="user-traffic"></a>Tráfego do usuário

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

### <a name="latency-distribution-of-questions"></a>Distribuição de latência de perguntas

```kusto
//Latency distribution of questions
requests
| where url endswith "generateAnswer" and name startswith "POST"
| parse kind = regex url with *"(?i)knowledgebases/"KbId"/generateAnswer"
| project timestamp, id, name, resultCode, performanceBucket, KbId
| summarize count() by performanceBucket, KbId
```

### <a name="unanswered-questions"></a>Perguntas não respondidas

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
| where  score  == "0"
| project timestamp, KbId, question, answer, score 
| order  by timestamp  desc
```

## <a name="next-steps"></a>Passos seguintes

> [!div class="nextstepaction"]
> [Escolha capactiy](../tutorials/choosing-capacity-qnamaker-deployment.md)
