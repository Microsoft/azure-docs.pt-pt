---
title: incluir ficheiro
description: incluir ficheiro
author: ggailey777
ms.service: azure-functions
ms.topic: include
ms.date: 10/19/2018
ms.author: glenga
ms.custom: include file
ms.openlocfilehash: 9c51ce726545d1c64d69c86c36fc69ea43c3b882
ms.sourcegitcommit: 5397b08426da7f05d8aa2e5f465b71b97a75550b
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 01/19/2020
ms.locfileid: "76279412"
---
Especifica quantas invocações de função são agregadas ao [calcular métricas para Application insights](../articles/azure-functions/functions-monitoring.md#configure-the-aggregator). 

```json
{
    "aggregator": {
        "batchSize": 1000,
        "flushTimeout": "00:00:30"
    }
}
```

|Propriedade |Predefinição  | Descrição |
|---------|---------|---------| 
|batchSize|1000|Número máximo de solicitações a serem agregadas.| 
|flushTimeout|00:00:30|Período de tempo máximo para agregação.| 

As invocações de função são agregadas quando o primeiro dos dois limites são atingidos.
