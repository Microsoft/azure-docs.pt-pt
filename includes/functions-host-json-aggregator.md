---
title: incluir ficheiro
description: incluir ficheiro
author: ggailey777
ms.service: azure-functions
ms.topic: include
ms.date: 10/19/2018
ms.author: glenga
ms.custom: include file
ms.openlocfilehash: c63cee1c451918569e9b7aa2e76209e8069d86ac
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/19/2021
ms.locfileid: "92167662"
---
Especifica quantas invocações de função são agregadas ao [calcular métricas para Insights de Aplicação](../articles/azure-functions/configure-monitoring.md#configure-the-aggregator). 

```json
{
    "aggregator": {
        "batchSize": 1000,
        "flushTimeout": "00:00:30"
    }
}
```

|Propriedade |Predefinição  | Description |
|---------|---------|---------| 
|batchSize|1000|Número máximo de pedidos de agregação.| 
|flushTimeout|00:00:30|Período máximo de tempo para agregar.| 

As invocações de funções são agregadas quando o primeiro dos dois limites é atingido.
