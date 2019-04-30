---
ms.openlocfilehash: b5d8f67a70961aab21312b6f241081dcb33f66fb
ms.sourcegitcommit: 61c8de2e95011c094af18fdf679d5efe5069197b
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/23/2019
ms.locfileid: "62104454"
---
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
|maxBatchSize|64|A contagem máxima do evento recebida por loop de recebimento.|
|prefetchCount|n/d|A predefinição PrefetchCount que será utilizada pelo EventProcessorHost subjacente.| 
|batchCheckpointFrequency|1|O número de lotes de eventos para processar antes de criar um ponto de verificação de cursor de EventHub.| 

