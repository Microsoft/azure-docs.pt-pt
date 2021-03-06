---
author: ggailey777
ms.service: azure-functions
ms.topic: include
ms.date: 09/04/2018
ms.author: glenga
ms.openlocfilehash: fe80a71125d43220e408eab7b07aeedcafa0a526
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/29/2021
ms.locfileid: "102473650"
---
### <a name="functions-2x-and-higher"></a>Funções 2.x e superior

```json
{
    "version": "2.0",
    "extensions": {
        "eventHubs": {
            "batchCheckpointFrequency": 5,
            "eventProcessorOptions": {
                "maxBatchSize": 256,
                "prefetchCount": 512
            },
            "initialOffsetOptions": {
                "type": "fromStart",
                "enqueuedTimeUtc": ""
            }
        }
    }
}  
```

|Propriedade  |Predefinição | Description |
|---------|---------|---------|
|batchCheckpointFrequency|1|O número de lotes de eventos a processar antes de criar um ponto de verificação do cursor EventHub.|
|EventProcessorOptions/maxBatchSize|10|A contagem máxima de evento recebida por ciclo de receção.|
|EventProcessorOptions/prefetchCount|300|A contagem de pré-irbuso predefinido utilizada pelo subjacente `EventProcessorHost` . O valor mínimo permitido é de 10.|
|Pré-partidas Pré-eliminatóriasOptions/tipo<sup>1</sup>|fromStart|A localização no fluxo de eventos a partir do qual começar a processar quando um ponto de verificação não existe no armazenamento. As opções `fromStart` são, `fromEnd` ou `fromEnqueuedTime` . `fromEnd` processa novos eventos que foram encadeados após o início da aplicação de função. Aplica-se a todas as divisórias.  Para obter mais informações, consulte a [documentação eventProcessorOptions](/dotnet/api/microsoft.azure.eventhubs.processor.eventprocessoroptions.initialoffsetprovider).|
|iniciaisOffsetOptions/enqueuedTimeUtc<sup>1</sup>|N/D| Especifica o tempo encadeado do evento no fluxo a partir do qual começar a processar. Quando `initialOffsetOptions/type` estiver configurado como `fromEnqueuedTime` , esta definição é obrigatória. Suporta o tempo em qualquer formato suportado por [DateTime.Parse()](/dotnet/standard/base-types/parsing-datetime)( tais como  `2020-10-26T20:31Z` . Para maior clareza, deve especificar um timezone. Quando o timezone não é especificado, as funções assumem o tempomozono local da máquina que executa a aplicação de função, que é UTC quando funciona no Azure. Para obter mais informações, consulte a [documentação eventProcessorOptions](/dotnet/api/microsoft.azure.eventhubs.processor.eventprocessoroptions.initialoffsetprovider).|

<sup>1</sup> Suporte para `intitialOffsetOptions` início com [EventHubs v4.2.0](https://github.com/Azure/azure-functions-eventhubs-extension/releases/tag/v4.2.0).

> [!NOTE]
> Para obter uma referência de host.jsem Funções Azure 2.x e não só, consulte [host.jsem referência para Funções Azure](../articles/azure-functions/functions-host-json.md).

### <a name="functions-1x"></a>Funções 1.x

```json
{
    "eventHub": {
      "maxBatchSize": 64,
      "prefetchCount": 256,
      "batchCheckpointFrequency": 1
    }
}
```

|Propriedade  |Predefinição | Description |
|---------|---------|---------| 
|maxBatchSize|64|A contagem máxima de evento recebida por ciclo de receção.|
|prefetchCount|n/a|A pré-busca predefinida que será usada pelo subjacente `EventProcessorHost` .| 
|batchCheckpointFrequency|1|O número de lotes de eventos a processar antes de criar um ponto de verificação do cursor EventHub.| 

> [!NOTE]
> Para obter uma referência de host.jsem Azure Funções 1.x, consulte [host.jsem referência para as Funções Azure 1.x](../articles/azure-functions/functions-host-json-v1.md).
