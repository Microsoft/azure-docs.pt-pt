---
author: ggailey777
ms.service: azure-functions
ms.topic: include
ms.date: 09/04/2018
ms.author: glenga
ms.openlocfilehash: 8f3a58d3a7470867ab23249bbd645289e010ad89
ms.sourcegitcommit: a43a59e44c14d349d597c3d2fd2bc779989c71d7
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 11/25/2020
ms.locfileid: "95997319"
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
|prefetchCount|300|A contagem de pré-irbuso predefinido utilizada pelo subjacente `EventProcessorHost` . O valor mínimo permitido é de 10.|
|batchCheckpointFrequency|1|O número de lotes de eventos a processar antes de criar um ponto de verificação do cursor EventHub.|

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

|Propriedade  |Predefinição | Descrição |
|---------|---------|---------| 
|maxBatchSize|64|A contagem máxima de evento recebida por ciclo de receção.|
|prefetchCount|n/a|A pré-busca predefinida que será usada pelo subjacente `EventProcessorHost` .| 
|batchCheckpointFrequency|1|O número de lotes de eventos a processar antes de criar um ponto de verificação do cursor EventHub.| 

> [!NOTE]
> Para obter uma referência de host.jsem Azure Funções 1.x, consulte [host.jsem referência para as Funções Azure 1.x](../articles/azure-functions/functions-host-json-v1.md).
