---
author: ggailey777
ms.service: azure-functions
ms.topic: include
ms.date: 09/04/2018
ms.author: glenga
ms.openlocfilehash: 2604a1608f21d7239db755027e15b8198fb3f9f2
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/28/2020
ms.locfileid: "81791665"
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
            }
        }
    }
}  
```

|Propriedade  |Predefinição | Descrição |
|---------|---------|---------|
|maxBatchSize|10|A contagem máxima de evento recebida por ciclo de receção.|
|pré-fetchCount|300|A contagem pre-fetch predefinida `EventProcessorHost`utilizada pelo subjacente .|
|loteCheckpointFrequency|1|O número de lotes de eventos para processar antes de criar um ponto de verificação de cursor EventHub.|

> [!NOTE]
> Para uma referência do host.json in Azure Functions 2.x e além, consulte a [referência host.json para funções azure](../articles/azure-functions/functions-host-json.md).

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

|Propriedade  |Predefinição | Descrição |
|---------|---------|---------| 
|maxBatchSize|64|A contagem máxima de evento recebida por ciclo de receção.|
|pré-fetchCount|n/d|A pré-busca predefinida que será `EventProcessorHost`utilizada pelo subjacente .| 
|loteCheckpointFrequency|1|O número de lotes de eventos para processar antes de criar um ponto de verificação de cursor EventHub.| 

> [!NOTE]
> Para uma referência do host.json in Azure Functions 1.x, consulte a [referência host.json para as funções Azure 1.x](../articles/azure-functions/functions-host-json-v1.md).

