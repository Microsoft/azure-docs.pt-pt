---
author: ggailey777
ms.service: azure-functions
ms.topic: include
ms.date: 06/15/2020
ms.author: glenga
ms.openlocfilehash: 906413d0a6702e6146779f79d628b5cebf383af1
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/29/2021
ms.locfileid: "92165775"
---
| | |
|--|--|
|**`<DESTINATION>`**| O destino para o qual são enviados registos. Valores válidos são `AppInsights` e `Blob` .<br/>Quando `AppInsights` utilizar, certifique-se de que [os Insights de Aplicação estão ativados na aplicação de função](../articles/azure-functions/configure-monitoring.md#enable-application-insights-integration).<br/>Quando define o destino para `Blob` , os registos são criados num recipiente de bolhas nomeado `azure-functions-scale-controller` na conta de armazenamento padrão definida na definição de `AzureWebJobsStorage` aplicação. |
|**`<VERBOSITY>`** | Especifica o nível de registo. Os valores suportados `None` `Warning` são, `Verbose` e.<br/>Quando definido `Verbose` para , o controlador de escala regista uma razão para cada alteração na contagem de trabalhadores, bem como informações sobre os gatilhos que fator nessas decisões. Os registos verbose incluem avisos de disparo e os hashes utilizados pelos gatilhos antes e depois do controlador de escala. |

> [!TIP]
> Tenha em mente que, ao deixar a registo do controlador de escala ativada, tem impacto nos [custos potenciais de monitorização da sua aplicação de função](../articles/azure-functions/functions-monitoring.md#application-insights-pricing-and-limits). Considere ativar o registo até ter recolhido dados suficientes para entender como o controlador de escala se está a comportar e, em seguida, desativá-lo.