---
author: ggailey777
ms.service: azure-functions
ms.topic: include
ms.date: 09/04/2018
ms.author: glenga
ms.openlocfilehash: b5d8f67a70961aab21312b6f241081dcb33f66fb
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/28/2020
ms.locfileid: "67184053"
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
|maxBatchSize|64|A contagem máxima de evento recebida por ciclo de receção.|
|pré-fetchCount|n/d|O PrefetchCount prefetchCount predefinido que será usado pelo Anfitrião do Processador de Eventos subjacente.| 
|loteCheckpointFrequency|1|O número de lotes de eventos para processar antes de criar um ponto de verificação de cursor EventHub.| 
