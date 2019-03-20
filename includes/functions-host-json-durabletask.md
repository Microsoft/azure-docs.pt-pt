---
title: incluir ficheiro
description: incluir ficheiro
services: functions
author: ggailey777
manager: jeconnoc
ms.service: functions
ms.topic: include
ms.date: 03/14/2019
ms.author: glenga
ms.custom: include file
ms.openlocfilehash: d79d1bd5ec244ad4399a02c349e2504516d06ccd
ms.sourcegitcommit: 5839af386c5a2ad46aaaeb90a13065ef94e61e74
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/19/2019
ms.locfileid: "58137633"
---
Definições de configuração para [funções duráveis](../articles/azure-functions/durable-functions-overview.md).

```json
{
  "durableTask": {
    "hubName": "MyTaskHub",
    "controlQueueBatchSize": 32,
    "partitionCount": 4,
    "controlQueueVisibilityTimeout": "00:05:00",
    "workItemQueueVisibilityTimeout": "00:05:00",
    "maxConcurrentActivityFunctions": 10,
    "maxConcurrentOrchestratorFunctions": 10,
    "maxQueuePollingInterval": "00:00:30",
    "azureStorageConnectionStringName": "AzureWebJobsStorage",
    "trackingStoreConnectionStringName": "TrackingStorage",
    "trackingStoreNamePrefix": "DurableTask",
    "traceInputsAndOutputs": false,
    "logReplayEvents": false,
    "eventGridTopicEndpoint": "https://topic_name.westus2-1.eventgrid.azure.net/api/events",
    "eventGridKeySettingName":  "EventGridKey",
    "eventGridPublishRetryCount": 3,
    "eventGridPublishRetryInterval": "00:00:30",
    "eventGridPublishEventTypes": ["Started", "Pending", "Failed", "Terminated"]
  }
}
```

Os nomes de hubs de tarefas devem começar com uma letra e consistir apenas letras e números. Se não for especificado, o nome do hub de tarefas padrão para uma aplicação de funções é **DurableFunctionsHub**. Para obter mais informações, consulte [os hubs de tarefas](../articles/azure-functions/durable-functions-task-hubs.md).

|Propriedade  |Predefinição | Descrição |
|---------|---------|---------|
|hubName|DurableFunctionsHub|Alternativo [hub tarefas](../articles/azure-functions/durable-functions-task-hubs.md) nomes podem ser utilizados para isolar várias aplicações de funções durável entre si, mesmo se estiver a utilizar o mesmo back-end de armazenamento.|
|controlQueueBatchSize|32|O número de mensagens para solicitar a partir da fila de controle de cada vez.|
|partitionCount |4|O número de partições para a fila de controle. Pode ser um número inteiro positivo entre 1 e 16.|
|controlQueueVisibilityTimeout |5 minutos|O tempo limite de visibilidade de mensagens de fila de retirada da fila de controle.|
|workItemQueueVisibilityTimeout |5 minutos|O tempo limite de visibilidade de mensagens de fila de item de trabalho retirada da fila.|
|maxConcurrentActivityFunctions |10 vezes o número de processadores no computador atual|O número máximo de funções de atividade que podem ser processadas em simultâneo numa instância de anfitrião único.|
|maxConcurrentOrchestratorFunctions |10 vezes o número de processadores no computador atual|O número máximo de funções do orchestrator que podem ser processadas em simultâneo numa instância de anfitrião único.|
|maxQueuePollingInterval|30 segundos|O máximo controlo e o intervalo de consulta de fila de item de trabalho no *hh: mm:* formato. Valores mais altos podem resultar em latências de processamento de mensagens superior. Valores mais baixos podem resultar em custos de armazenamento superiores, devido às transações de armazenamento maior.|
|azureStorageConnectionStringName |AzureWebJobsStorage|O nome da definição de aplicação que tenha a cadeia de ligação de armazenamento do Azure utilizada para gerir os recursos de armazenamento do Azure subjacentes.|
|trackingStoreConnectionStringName||O nome de uma cadeia de ligação a utilizar para as tabelas de histórico e instâncias. Se não for especificado, o `azureStorageConnectionStringName` ligação é utilizada.|
|trackingStoreNamePrefix||O prefixo a utilizar para as instâncias e o histórico de tabelas quando `trackingStoreConnectionStringName` está especificado. Se não definir o valor de prefixo predefinido será `DurableTask`. Se `trackingStoreConnectionStringName` não for especificada, em seguida, irão utilizar as tabelas de histórico e as instâncias do `hubName` valor como o prefixo e qualquer definição para `trackingStoreNamePrefix` serão ignoradas.|
|traceInputsAndOutputs |false|Um valor que indica se pretende rastrear as entradas e saídas de chamadas de função. É o comportamento predefinido durante o rastreamento de eventos de execução de função incluir o número de bytes na serializada entradas e saídas de chamadas de função. Esse comportamento fornece informações mínimas sobre as entradas e saídas aparência sem bloating os registos ou inadvertidamente expor informações confidenciais. Definir esta propriedade como true faz com que o registo de função predefinido para todo o conteúdo da função entradas e saídas de registo.|
|logReplayEvents|false|Um valor que indica se deve escrever eventos de repetição de orquestração no Application Insights.|
|eventGridTopicEndpoint ||O URL de um ponto de extremidade de um tópico personalizado do Azure Event Grid. Quando essa propriedade é definida, os eventos de notificação de ciclo de vida de orquestração são publicados para este ponto final. Esta propriedade suporta a resolução de definições da aplicação.|
|eventGridKeySettingName ||O nome da definição de aplicação que contém a chave utilizada para autenticar com o tópico personalizado do Azure Event Grid em `EventGridTopicEndpoint`.|
|eventGridPublishRetryCount|0|O número de vezes para repetir se publicar para o tópico do Event Grid falha.|
|eventGridPublishRetryInterval|5 minutos|O Event Grid publica o intervalo de repetição no *hh: mm:* formato.|
|eventGridPublishEventTypes||Uma lista de tipos de eventos para publicar no Event Grid. Se não for especificado, todos os tipos de eventos serão publicados. Permissão de valores incluem `Started`, `Completed`, `Failed`, `Terminated`.|

Muitas destas definições são para otimizar o desempenho. Para obter mais informações, consulte [desempenho e dimensionamento](../articles/azure-functions/durable-functions-perf-and-scale.md).