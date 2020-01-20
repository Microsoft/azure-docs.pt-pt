---
title: incluir ficheiro
description: incluir ficheiro
author: ggailey777
ms.service: azure-functions
ms.topic: include
ms.date: 10/19/2018
ms.author: glenga
ms.custom: include file
ms.openlocfilehash: a925076dfccd30c73febb2aadc8692667ea01525
ms.sourcegitcommit: 5397b08426da7f05d8aa2e5f465b71b97a75550b
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 01/19/2020
ms.locfileid: "76279486"
---
Controla o [recurso de amostragem no Application insights](../articles/azure-functions/functions-monitoring.md#configure-sampling).

```json
{
    "applicationInsights": {
        "sampling": {
          "isEnabled": true,
          "maxTelemetryItemsPerSecond" : 5
        }
    }
}
```

|Propriedade  |Predefinição | Descrição |
|---------|---------|---------| 
|isEnabled|true|Habilita ou desabilita a amostragem.| 
|maxTelemetryItemsPerSecond|5|O limite no qual a amostragem começa.| 
