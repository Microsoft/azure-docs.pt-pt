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
ms.openlocfilehash: eca2d3359614875e5bff0c9bb67f006f0a8cdba1
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/28/2020
ms.locfileid: "77198331"
---
## <a name="function-app-timeout-duration"></a><a name="timeout"></a>Duração do tempo de tempo fora da aplicação de função 

A duração do tempo de saída `functionTimeout` de uma aplicação de função é definida pela propriedade no ficheiro do projeto [host.json.](../articles/azure-functions/functions-host-json.md#functiontimeout) O quadro seguinte mostra os valores padrão e máximos em minutos para ambos os planos e as diferentes versões de tempo de execução:

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
> Independentemente da definição de tempo limite da aplicação da função, 230 segundos é o máximo de tempo que uma função desencadeada em HTTP pode demorar a responder a um pedido. Isto deve-se ao [tempo inativo do Equilíbrio de Carga Azure](../articles/app-service/faq-availability-performance-application-issues.md#why-does-my-request-time-out-after-230-seconds). Para tempos de processamento mais longos, considere utilizar o [padrão assinizador das Funções Duráveis](../articles/azure-functions/durable/durable-functions-overview.md#async-http) ou [adiar o trabalho real e devolver uma resposta imediata](../articles/azure-functions/functions-best-practices.md#avoid-long-running-functions).
