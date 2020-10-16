---
author: ggailey777
ms.service: azure-functions
ms.topic: include
ms.date: 06/15/2020
ms.author: glenga
ms.openlocfilehash: 49818cf59da2d63cef4bb0bdca38d38a2feafca5
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/09/2020
ms.locfileid: "86169918"
---
| | |
|--|--|
|**`<DESTINATION>`**| O destino para o qual são enviados registos. Valores válidos são `AppInsights` e `Blob` .<br/>Quando `AppInsights` utilizar, certifique-se de que [os Insights de Aplicação estão ativados na aplicação de função](../articles/azure-functions/functions-monitoring.md#enable-application-insights-integration).<br/>Quando define o destino para `Blob` , os registos são criados num recipiente de bolhas nomeado `azure-functions-scale-controller` na conta de armazenamento padrão definida na definição de `AzureWebJobsStorage` aplicação. |
|**`<VERBOSITY>`** | Especifica o nível de registo. Os valores suportados `None` `Warning` são, `Verbose` e.<br/>Quando definido `Verbose` para , o controlador de escala regista uma razão para cada alteração na contagem de trabalhadores, bem como informações sobre os gatilhos que fator nessas decisões. Os registos verbose incluem avisos de disparo e os hashes utilizados pelos gatilhos antes e depois do controlador de escala. |

> [!CAUTION]
> Não deixe o registo do controlador de escala ativado. Ativar o registo até ter recolhido dados suficientes para perceber como o controlador de escala se está a comportar e, em seguida, desativá-lo.