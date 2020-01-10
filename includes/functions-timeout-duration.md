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
ms.openlocfilehash: 3501ff3f92ae045019df2766bbcf7fc2c3fec5b0
ms.sourcegitcommit: aee08b05a4e72b192a6e62a8fb581a7b08b9c02a
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 01/09/2020
ms.locfileid: "75768928"
---
## <a name="timeout"></a>Duração do tempo limite do aplicativo de funções 

A duração do tempo limite de um aplicativo de funções é definida pela propriedade `functionTimeout` no arquivo de projeto [host. JSON](../articles/azure-functions/functions-host-json.md#functiontimeout) . A tabela a seguir mostra os valores padrão e máximo em minutos para os planos e para as diferentes versões de tempo de execução:

| Plano | Versão de tempo de execução | Predefinição | Máximo |
|------|---------|---------|---------|
| Consumo | 1.x | 5 | 10 |
| Consumo | 2.x | 5 | 10 |
| Consumo | Win | 5 | 10 |
| Serviço de Aplicações | 1.x | Ilimitado | Ilimitado |
| Serviço de Aplicações | 2.x | 30 | Ilimitado |
| Serviço de Aplicações | Win | 30 | Ilimitado |

> [!NOTE] 
> Independentemente da configuração do tempo limite do aplicativo de funções, 230 segundos é a quantidade máxima de tempo que uma função disparada por HTTP pode levar para responder a uma solicitação. Isso ocorre devido ao [tempo limite de ociosidade padrão de Azure Load Balancer](../articles/app-service/faq-availability-performance-application-issues.md#why-does-my-request-time-out-after-230-seconds). Para tempos de processamento mais longos, considere usar o [padrão assíncrono Durable Functions](../articles/azure-functions/durable/durable-functions-overview.md#async-http) ou [adiar o trabalho real e retornar uma resposta imediata](../articles/azure-functions/functions-best-practices.md#avoid-long-running-functions).
