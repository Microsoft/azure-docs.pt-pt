---
title: incluir ficheiro
description: incluir ficheiro
services: functions
author: nzthiago
ms.service: azure-functions
ms.topic: include
ms.date: 02/21/2018
ms.author: nzthiago
ms.custom: include file
ms.openlocfilehash: ffb29fc76313e8870b52cb0a63936da7853ea6ce
ms.sourcegitcommit: 8a59b051b283a72765e7d9ac9dd0586f37018d30
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/20/2019
ms.locfileid: "58305096"
---
## <a name="timeout"></a>Duração do tempo limite de aplicação de função 

A duração de tempo limite de uma aplicação de funções é definida pela propriedade functionTimeout no [Host. JSON](../articles/azure-functions/functions-host-json.md#functiontimeout) arquivo de projeto. A tabela seguinte mostra os valores padrão e o máximo em minutos para ambos os planos e em ambas as versões de tempo de execução:

| Planear | Versão de Runtime | Predefinição | Máximo |
|------|---------|---------|---------|
| Consumo | 1.x | 5 | 10 |
| Consumo | 2.x | 5 | 10 |
| Serviço de Aplicações | 1.x | Ilimitado | Ilimitado |
| Serviço de Aplicações | 2.x | 30 | Ilimitado |

> [!NOTE] 
> Independentemente da definição de tempo limite de aplicação de função, 230 segundos é a quantidade máxima de tempo que uma função acionada por HTTP pode tomar para responder a um pedido. Isso é devido a [predefinido o tempo limite de inatividade do Balanceador de carga do Azure](../articles/app-service/faq-availability-performance-application-issues.md#why-does-my-request-time-out-after-230-seconds). Para mais tempos de processamento, considere a utilização a [padrão assíncrono de funções duráveis](../articles/azure-functions/durable/durable-functions-concepts.md#async-http) ou [diferir o trabalho real e devolver uma resposta imediata](../articles/azure-functions/functions-best-practices.md#avoid-long-running-functions).
