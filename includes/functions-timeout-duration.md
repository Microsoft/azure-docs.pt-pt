---
title: ficheiro de inclusão
description: ficheiro de inclusão
services: functions
author: nzthiago
ms.service: azure-functions
ms.topic: include
ms.date: 02/21/2018
ms.author: nzthiago
ms.custom: include file
ms.openlocfilehash: eca2d3359614875e5bff0c9bb67f006f0a8cdba1
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/09/2020
ms.locfileid: "77198331"
---
## <a name="function-app-timeout-duration"></a><a name="timeout"></a>Duração do tempo limite da aplicação da função 

A duração de tempo de uma aplicação de função é definida pela `functionTimeout` propriedade nohost.js[ no](../articles/azure-functions/functions-host-json.md#functiontimeout) ficheiro do projeto. A tabela a seguir mostra os valores predefinidos e máximos em minutos tanto para os planos como para as diferentes versões de tempo de execução:

| Planear | Versão runtime | Predefinição | Máximo |
|------|---------|---------|---------|
| Consumo | 1.x | 5 | 10 |
| Consumo | 2.x | 5 | 10 |
| Consumo | 3.x | 5 | 10 |
| Premium | 1.x | 30 | Ilimitado |
| Premium | 2.x | 30 | Ilimitado |
| Premium | 3.x | 30 | Ilimitado |
| Serviço de Aplicações | 1.x | Ilimitado | Ilimitado |
| Serviço de Aplicações | 2.x | 30 | Ilimitado |
| Serviço de Aplicações | 3.x | 30 | Ilimitado |

> [!NOTE] 
> Independentemente da definição de tempo limite de tempo da aplicação da função, 230 segundos é o tempo máximo que uma função detonada HTTP pode demorar a responder a um pedido. Isto deve-se ao [tempo limite de marcha lenta padrão do Azure Load Balancer](../articles/app-service/faq-availability-performance-application-issues.md#why-does-my-request-time-out-after-230-seconds). Para tempos de processamento mais longos, considere utilizar o [padrão async async das Funções Duráveis](../articles/azure-functions/durable/durable-functions-overview.md#async-http) ou [adiar o trabalho real e devolver uma resposta imediata](../articles/azure-functions/functions-best-practices.md#avoid-long-running-functions).
