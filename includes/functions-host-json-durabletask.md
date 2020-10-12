---
title: ficheiro de inclusão
description: ficheiro de inclusão
author: ggailey777
ms.service: azure-functions
ms.topic: include
ms.date: 03/14/2019
ms.author: glenga
ms.custom: include file
ms.openlocfilehash: 6e253604c57d73c2a89ccfa5cff7efe9e572d11d
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/09/2020
ms.locfileid: "89094199"
---
Definições de configuração para [funções duradouras](../articles/azure-functions/durable-functions-overview.md).

### <a name="durable-functions-1x"></a>Funções duradouras 1.x

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
    "eventGridPublishEventTypes": ["Started", "Completed", "Failed", "Terminated"]
  }
}
```

### <a name="durable-functions-2x"></a><a name="durable-functions-2-0-host-json"></a>Funções duradouras 2.x

```json
{
 "extensions": {
  "durableTask": {
    "hubName": "MyTaskHub",
    "storageProvider": {
      "connectionStringName": "AzureWebJobsStorage",
      "controlQueueBatchSize": 32,
      "controlQueueBufferThreshold": 256,
      "controlQueueVisibilityTimeout": "00:05:00",
      "maxQueuePollingInterval": "00:00:30",
      "partitionCount": 4,
      "trackingStoreConnectionStringName": "TrackingStorage",
      "trackingStoreNamePrefix": "DurableTask",
      "useLegacyPartitionManagement": true,
      "workItemQueueVisibilityTimeout": "00:05:00",
    },
    "tracing": {
      "traceInputsAndOutputs": false,
      "traceReplayEvents": false,
    },
    "notifications": {
      "eventGrid": {
        "topicEndpoint": "https://topic_name.westus2-1.eventgrid.azure.net/api/events",
        "keySettingName": "EventGridKey",
        "publishRetryCount": 3,
        "publishRetryInterval": "00:00:30",
        "publishEventTypes": [
          "Started",
          "Pending",
          "Failed",
          "Terminated"
        ]
      }
    },
    "maxConcurrentActivityFunctions": 10,
    "maxConcurrentOrchestratorFunctions": 10,
    "extendedSessionsEnabled": false,
    "extendedSessionIdleTimeoutInSeconds": 30,
    "useAppLease": true,
    "useGracefulShutdown": false
  }
 }
}

```

Os nomes dos centros de tarefa devem começar com uma letra e consistir apenas em letras e números. Se não for especificado, o nome do centro de tarefas predefinido para uma aplicação de função é **DurableFunctionsHub**. Para obter mais informações, consulte [os centros de tarefas.](../articles/azure-functions/durable-functions-task-hubs.md)

|Propriedade  |Predefinição | Descrição |
|---------|---------|---------|
|nome hub|DurableFunctionsHub|Nomes de [centros de tarefas](../articles/azure-functions/durable-functions-task-hubs.md) alternativos podem ser usados para isolar várias aplicações de Funções Duráveis umas das outras, mesmo que estejam a usar o mesmo backend de armazenamento.|
|controlQueueBatchSize|32|O número de mensagens a retirar da fila de controlo de cada vez.|
|controlQueueBufferThreshold|256|O número de mensagens de fila de controlo que podem ser tamponadas na memória de cada vez, altura em que o despachante aguardará antes de descoduar quaisquer mensagens adicionais.|
|partitionCount |4|A contagem de divisórias para a fila de controlo. Pode ser um inteiro positivo entre 1 e 16.|
|controlQueueVisibilidadeTimeout |5 minutos|O tempo de visibilidade das mensagens de fila de controlo descamoso.|
|workItemQueueVisibilityTimeout |5 minutos|O tempo de visibilidade das mensagens de fila de artigos de trabalho descamoso.|
|maxConcurrentActivityFunctions |10X o número de processadores na máquina atual|O número máximo de funções de atividade que podem ser processadas simultaneamente numa única instância de hospedeiro.|
|maxConcurrentOrchestratorFunctions |10X o número de processadores na máquina atual|O número máximo de funções orquestradoras que podem ser processadas simultaneamente numa única instância de anfitrião.|
|maxQueuePollingInterval|30 segundos|O intervalo máximo de votação de fila de controlo e de trabalho no formato *hh:mm:ss.* Valores mais elevados podem resultar em atrasos de processamento de mensagens mais elevados. Valores mais baixos podem resultar em custos de armazenamento mais elevados devido ao aumento das transações de armazenamento.|
|azureStorageConnectionStringName |AzureWebJobsStorage|O nome da definição da aplicação que tem a cadeia de conexão Azure Storage utilizada para gerir os recursos de Armazenamento Azure subjacentes.|
|trackingStoreConnectionStringName||O nome de uma cadeia de ligação a utilizar para as tabelas História e Instâncias. Se não for especificada, utiliza-se a `connectionStringName` ligação (Durável `azureStorageConnectionStringName` 2.x) ou (Durável 1.x).|
|trackingStoreNamePrefix||O prefixo a utilizar para as tabelas História e Instâncias quando `trackingStoreConnectionStringName` especificado. Se não for definido, o valor prefixo prefixo prefixo prefixo padrão será `DurableTask` . Se `trackingStoreConnectionStringName` não for especificado, as tabelas História e Instâncias utilizarão o `hubName` valor como seu prefixo, e qualquer definição para `trackingStoreNamePrefix` será ignorada.|
|traceInputsAndOutputs |false|Um valor que indica se deve rastrear as entradas e saídas das chamadas de funções. O comportamento predefinido ao rastrear eventos de execução de funções é incluir o número de bytes nas entradas serializadas e saídas para chamadas de função. Este comportamento fornece informações mínimas sobre como são as entradas e saídas sem inchar os registos ou expor inadvertidamente informações sensíveis. A definição desta propriedade para verdadeira causa a função padrão de registo de todo o conteúdo das entradas e saídas de funções.|
|logReplayEvents|false|Um valor que indica se deve escrever eventos de repetição de orquestração para Application Insights.|
|eventGridTopicEndpoint ||O URL de um ponto final de tópico personalizado Azure Event Grid. Quando esta propriedade está definida, os eventos de notificação do ciclo de vida da orquestração são publicados neste ponto final. Esta propriedade suporta a resolução de Definições de Aplicação.|
|eventGridKeySettingName ||O nome da definição da aplicação que contém a chave utilizada para autenticar com o tópico personalizado Azure Event Grid em `EventGridTopicEndpoint` .|
|eventGridPublishRetryCount|0|O número de vezes para voltar a tentar se a publicação no Tópico de Grelha de Eventos falhar.|
|eventGridPublishRetryInterval|5 minutos|A Grade de Eventos publica o intervalo de repetição no formato *hh:mm:ss.*|
|eventGridPublishEventTypes||Uma lista de tipos de eventos para publicar na Grade de Eventos. Se não for especificado, todos os tipos de eventos serão publicados. Os valores permitidos `Started` incluem, , , . `Completed` `Failed` `Terminated` .|
|useAppLease|true|Quando definido para `true` , as aplicações exigirão adquirir um aluguer de blob ao nível de aplicações antes de processar mensagens do centro de tarefas. Para mais informações, consulte a [documentação de recuperação de desastres e geo-distribuição.](../articles/azure-functions/durable/durable-functions-disaster-recovery-geo-distribution.md) Disponível a partir de v2.3.0.
|useLegacyPartitionManagement|true|Quando `false` definido, utiliza um algoritmo de gestão de partição que reduz a possibilidade de duplicar a execução da função ao escalonar.  Disponível a partir de v2.3.0. O padrão será alterado para `false` uma futura versão.|
|useGracefulShutdown|false|(Pré-visualização) Ativar graciosamente desligar para reduzir as chances de paralisações do hospedeiro falhando execuções de função no processo.|

Muitas destas configurações são para otimizar o desempenho. Para mais informações, consulte [Performance e escala.](../articles/azure-functions/durable-functions-perf-and-scale.md)
