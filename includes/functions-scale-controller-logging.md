---
author: ggailey777
ms.service: azure-functions
ms.topic: include
ms.date: 06/15/2020
ms.author: glenga
ms.openlocfilehash: 4cbf179658ddc13aca9bf30934dc28ab0cf5fd9d
ms.sourcegitcommit: 3ee3045f6106175e59d1bd279130f4933456d5ff
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/31/2021
ms.locfileid: "106081143"
---
| Propriedade | Descrição |
|--|--|
|**`<DESTINATION>`**| O destino para o qual são enviados registos. Valores válidos são `AppInsights` e `Blob` .<br/>Quando `AppInsights` utilizar, certifique-se de que [os Insights de Aplicação estão ativados na aplicação de função](../articles/azure-functions/configure-monitoring.md#enable-application-insights-integration).<br/>Quando define o destino para `Blob` , os registos são criados num recipiente de bolhas nomeado `azure-functions-scale-controller` na conta de armazenamento padrão definida na definição de `AzureWebJobsStorage` aplicação. |
|**`<VERBOSITY>`** | Especifica o nível de registo. Os valores suportados `None` `Warning` são, `Verbose` e.<br/>Quando definido `Verbose` para , o controlador de escala regista uma razão para cada alteração na contagem de trabalhadores, bem como informações sobre os gatilhos que fator nessas decisões. Os registos verbose incluem avisos de disparo e os hashes utilizados pelos gatilhos antes e depois do controlador de escala. |

> [!TIP]
> Tenha em mente que, ao deixar a registo do controlador de escala ativada, tem impacto nos [custos potenciais de monitorização da sua aplicação de função](../articles/azure-functions/functions-monitoring.md#application-insights-pricing-and-limits). Considere ativar o registo até ter recolhido dados suficientes para entender como o controlador de escala se está a comportar e, em seguida, desativá-lo.
