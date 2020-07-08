---
title: ficheiro de inclusão
description: ficheiro de inclusão
author: ggailey777
ms.service: azure-functions
ms.topic: include
ms.date: 10/19/2018
ms.author: glenga
ms.custom: include file
ms.openlocfilehash: 9c51ce726545d1c64d69c86c36fc69ea43c3b882
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 07/02/2020
ms.locfileid: "76279412"
---
Especifica quantas invocações de função são agregadas ao [calcular métricas para Insights de Aplicação](../articles/azure-functions/functions-monitoring.md#configure-the-aggregator). 

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
|batchSize|1000|Número máximo de pedidos de agregação.| 
|flushTimeout|00:00:30|Período máximo de tempo para agregar.| 

As invocações de funções são agregadas quando o primeiro dos dois limites é atingido.
