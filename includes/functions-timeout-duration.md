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
ms.openlocfilehash: 47c2429363945cf1529cee6e49947aaea95b7022
ms.sourcegitcommit: b4f201a633775fee96c7e13e176946f6e0e5dd85
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/18/2019
ms.locfileid: "72597419"
---
## <a name="timeout"></a>Duração do tempo limite do aplicativo de funções 

A duração do tempo limite de um aplicativo de funções é definida pela propriedade functionTimeout no arquivo de projeto [host. JSON](../articles/azure-functions/functions-host-json.md#functiontimeout) . A tabela a seguir mostra os valores padrão e máximo em minutos para ambos os planos e em ambas as versões de tempo de execução:

| Plano | Versão de tempo de execução | Predefinição | Máximo |
|------|---------|---------|---------|
| Consumo | 1.x | 5 | 10 |
| Consumo | 2.x | 5 | 10 |
| Consumo | 3. x (visualização) | 5 | 10 |
| Serviço de Aplicações | 1.x | Ilimitado | Ilimitado |
| Serviço de Aplicações | 2.x | 30 | Ilimitado |
| Serviço de Aplicações | 3. x (visualização) | 30 | Ilimitado |

> [!NOTE] 
> Independentemente da configuração do tempo limite do aplicativo de funções, 230 segundos é a quantidade máxima de tempo que uma função disparada por HTTP pode levar para responder a uma solicitação. Isso ocorre devido ao [tempo limite de ociosidade padrão de Azure Load Balancer](../articles/app-service/faq-availability-performance-application-issues.md#why-does-my-request-time-out-after-230-seconds). Para tempos de processamento mais longos, considere usar o [padrão assíncrono Durable Functions](../articles/azure-functions/durable/durable-functions-overview.md#async-http) ou [adiar o trabalho real e retornar uma resposta imediata](../articles/azure-functions/functions-best-practices.md#avoid-long-running-functions).
