---
title: Análise sobre a base de conhecimentos
titleSuffix: Azure Cognitive Services
description: QnA Maker armazenará todos os logs de chat e outras telemetrias, se você tiver habilitado o Application insights durante a criação de seu serviço de QnA Maker. Execute as consultas de exemplo para fazer seus logs de chat do App insights.
services: cognitive-services
author: diberry
manager: nitinme
displayName: chat history, history, chat logs, logs
ms.service: cognitive-services
ms.subservice: qna-maker
ms.topic: article
ms.date: 07/16/2019
ms.author: diberry
ms.openlocfilehash: 5fc473fb1a1b1af84b0966bde4ecf02f4f221bf1
ms.sourcegitcommit: a8b638322d494739f7463db4f0ea465496c689c6
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 07/17/2019
ms.locfileid: "68296411"
---
# <a name="get-analytics-on-your-knowledge-base"></a>Obter análises da base de dados de conhecimento

QnA Maker armazenará todos os logs de chat e outras telemetrias, se você tiver habilitado o Application insights durante a [criação de seu serviço de QnA Maker](./set-up-qnamaker-service-azure.md). Execute as consultas de exemplo para fazer seus logs de chat do App insights.

1. Vá para o recurso do App insights.

    ![Selecione o recurso do Application insights](../media/qnamaker-how-to-analytics-kb/resources-created.png)

2. Selecione **análise**. Uma nova janela é aberta, onde você pode consultar QnA Maker telemetria.

    ![Selecionar análise](../media/qnamaker-how-to-analytics-kb/analytics.png)

3. Cole a consulta a seguir e execute-a.

    ```query
    requests
    | where url endswith "generateAnswer"
    | project timestamp, id, name, resultCode, duration, performanceBucket
    | parse kind = regex name with *"(?i)knowledgebases/"KbId"/generateAnswer"
    | join kind= inner (
    traces | extend id = operation_ParentId
    ) on id
    | extend question = tostring(customDimensions['Question'])
    | extend answer = tostring(customDimensions['Answer'])
    | extend score = tostring(customDimensions['Score'])
    | project timestamp, resultCode, duration, id, question, answer, score, performanceBucket,KbId 
    ```

    Selecione **executar** para executar a consulta.

    ![Executar consulta](../media/qnamaker-how-to-analytics-kb/run-query.png)

## <a name="run-queries-for-other-analytics-on-your-qna-maker-knowledge-base"></a>Executar consultas para outras análises em sua base de dados de conhecimento QnA Maker

### <a name="total-90-day-traffic"></a>Total de tráfego de 90 dias

```query
    //Total Traffic
    requests
    | where url endswith "generateAnswer" and name startswith "POST"
    | parse kind = regex name with *"(?i)knowledgebases/"KbId"/generateAnswer" 
    | summarize ChatCount=count() by bin(timestamp, 1d), KbId
```

### <a name="total-question-traffic-in-a-given-time-period"></a>Tráfego de pergunta total em um determinado período de tempo

```query
    //Total Question Traffic in a given time period
    let startDate = todatetime('2018-02-18');
    let endDate = todatetime('2018-03-12');
    requests
    | where timestamp <= endDate and timestamp >=startDate
    | where url endswith "generateAnswer" and name startswith "POST" 
    | parse kind = regex name with *"(?i)knowledgebases/"KbId"/generateAnswer" 
    | summarize ChatCount=count() by KbId
```

### <a name="user-traffic"></a>Tráfego do usuário

```query
    //User Traffic
    requests
    | where url endswith "generateAnswer"
    | project timestamp, id, name, resultCode, duration
    | parse kind = regex name with *"(?i)knowledgebases/"KbId"/generateAnswer"
    | join kind= inner (
    traces | extend id = operation_ParentId 
    ) on id
    | extend UserId = tostring(customDimensions['UserId'])
    | summarize ChatCount=count() by bin(timestamp, 1d), UserId, KbId
```

### <a name="latency-distribution-of-questions"></a>Distribuição de latência de perguntas

```query
    //Latency distribution of questions
    requests
    | where url endswith "generateAnswer" and name startswith "POST"
    | parse kind = regex name with *"(?i)knowledgebases/"KbId"/generateAnswer"
    | project timestamp, id, name, resultCode, performanceBucket, KbId
    | summarize count() by performanceBucket, KbId
```

## <a name="next-steps"></a>Passos seguintes

> [!div class="nextstepaction"]
> [Gerenciar chaves](./key-management.md)
