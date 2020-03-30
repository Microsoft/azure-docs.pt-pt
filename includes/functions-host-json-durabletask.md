---
title: incluir ficheiro
description: incluir ficheiro
author: ggailey777
ms.service: azure-functions
ms.topic: include
ms.date: 03/14/2019
ms.author: glenga
ms.custom: include file
ms.openlocfilehash: 6bb59db4c1b31033b1e116742dedc94621b1c60d
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/28/2020
ms.locfileid: "80117225"
---
Definições de configuração para [funções duráveis](../articles/azure-functions/durable-functions-overview.md).

### <a name="durable-functions-1x"></a>Funções Duráveis 1.x

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

### <a name="durable-functions-2x"></a><a name="durable-functions-2-0-host-json"></a>Funções Duráveis 2.x

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
    "useGracefulShutdown": false
  }
  }
}

```

Os nomes do centro de tarefas devem começar com uma letra e consistir apenas em letras e números. Se não especificado, o nome do centro de tarefas padrão para uma aplicação de função é **DurableFunctionsHub**. Para mais informações, consulte os [centros de tarefas.](../articles/azure-functions/durable-functions-task-hubs.md)

|Propriedade  |Predefinição | Descrição |
|---------|---------|---------|
|hubName|DurableFunctionsHub|Os nomes alternativos do centro de [tarefas](../articles/azure-functions/durable-functions-task-hubs.md) podem ser usados para isolar várias aplicações de Funções Duráveis umas das outras, mesmo que estejam a usar o mesmo backend de armazenamento.|
|controloQueueBatchSize|32|O número de mensagens a retirar da fila de controlo de cada vez.|
|controlEQueueBufferThreshold|256|O número de mensagens de fila de controlo que podem ser tamponadas na memória de cada vez, altura em que o despachante aguardará antes de desacarar quaisquer mensagens adicionais.|
|partitionCount |4|A contagem de divisórias para a fila de controlo. Pode ser um inteiro positivo entre 1 e 16.|
|controlQueueVisibilityTimeout |5 minutos|O tempo de visibilidade das mensagens de fila de controlo despachos.|
|trabalhoItemQueueVisibilityTimeout |5 minutos|O tempo de visibilidade das mensagens de fila de trabalho despacudas.|
|maxConcurrentActivityFunctions |10X o número de processadores na máquina atual|O número máximo de funções de atividade que podem ser processadas simultaneamente numa única instância de acolhimento.|
|maxConcurrentOrchestratorFunctions |10X o número de processadores na máquina atual|O número máximo de funções orquestradoras que podem ser processadas simultaneamente numa única instância de anfitrião.|
|maxQueuePollingInterval|30 segundos|O intervalo máximo de sondagens de controlo e de trabalho no formato *hh:mm:ss.* Valores mais elevados podem resultar em tardios de processamento de mensagens mais elevados. Valores mais baixos podem resultar em custos de armazenamento mais elevados devido ao aumento das transações de armazenamento.|
|azureStorageConnectionStringName |AzureWebJobsStorage|O nome da definição de aplicações que tem a cadeia de ligação de armazenamento Azure usada para gerir os recursos de armazenamento azure subjacentes.|
|trackingStoreConnectionStringName||O nome de uma cadeia de ligação para usar para as tabelas História e Instâncias. Se não especificada, a `azureStorageConnectionStringName` ligação é utilizada.|
|trackingStoreNamePrefix||O prefixo a utilizar para as `trackingStoreConnectionStringName` tabelas História e Instâncias quando é especificado. Se não for definido, o `DurableTask`valor prefixo predefinido por prefixo será . Se `trackingStoreConnectionStringName` não for especificado, as tabelas História `hubName` e Instâncias usarão o `trackingStoreNamePrefix` valor como prefixo, e qualquer definição para serão ignoradas.|
|traceInputsAndOutputs |false|Um valor que indique se deve rastrear as inputs e saídas das chamadas de função. O comportamento padrão ao rastrear eventos de execução de funções é incluir o número de bytes nas inputs e saídas serializadas para chamadas de função. Este comportamento fornece informações mínimas sobre como são as inputs e saídas sem inchar os registos ou expor inadvertidamente informações sensíveis. A definição desta propriedade como verdadeira faz com que a função predefinida de registo de registo de todo o conteúdo das inputações e saídas de função.|
|logReplayEvents|false|Um valor que indica se deve escrever eventos de reprodução de orquestração para Application Insights.|
|eventoGridTopicEndpoint ||O URL de um tópico personalizado da Grelha de Eventos Azure. Quando esta propriedade é definida, os eventos de notificação do ciclo de vida da orquestração são publicados neste ponto final. Esta propriedade suporta a resolução de Definições de Aplicações.|
|eventGridKeySettingName ||O nome da definição da aplicação que contém a chave utilizada `EventGridTopicEndpoint`para autenticar com o tópico personalizado da Rede de Eventos Azure em .|
|eventoGridPublishRetryCount|0|O número de vezes para voltar a tentar se a publicação para o Tópico da Grelha de Eventos falhar.|
|eventoGridPublishRetryInterval|5 minutos|A Grelha de Eventos publica intervalo de repetição no formato *hh:mm:ss.*|
|eventoSGridPublishEventTypes||Uma lista de tipos de eventos para publicar na Grelha de Eventos. Se não for especificado, todos os tipos de eventos serão publicados. Os valores `Completed` `Failed`permitidos incluem, `Terminated` `Started`.|
|useGracefulShutdown|false|(Pré-visualização) Permitir desligar graciosamente para reduzir a probabilidade de encerramentos de hospedeiros falhando as execuções de funções no processo.|

Muitas destas configurações são para otimizar o desempenho. Para mais informações, consulte [Performance e escala.](../articles/azure-functions/durable-functions-perf-and-scale.md)
