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
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/28/2020
ms.locfileid: "76279412"
---
Especifica quantas invocações de função são agregadas no cálculo das [métricas para Insights](../articles/azure-functions/functions-monitoring.md#configure-the-aggregator)de Aplicação . 

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
|batchSize|1000|Número máximo de pedidos a agregados.| 
|flushTimeout|00:00:30|Período máximo de tempo a agregado.| 

As invocações de funções são agregadas quando o primeiro dos dois limites é atingido.
