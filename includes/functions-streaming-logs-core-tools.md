---
author: ggailey777
ms.author: glenga
ms.date: 7/24/2019
ms.topic: include
ms.service: azure-functions
ms.openlocfilehash: 0159ceb6e5d6d64a7a9bda383396607e4ce05b84
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/29/2021
ms.locfileid: "96020373"
---
#### <a name="built-in-log-streaming"></a>Transmissão em fluxo de registos incorporada

Utilize a opção `logstream` para começar a receber registos de transmissão em fluxo de uma aplicação de funções específica em execução no Azure, como no exemplo seguinte:

```bash
func azure functionapp logstream <FunctionAppName>
```

>[!NOTE]
>O streaming de registos incorporado ainda não está ativado em Core Tools para aplicações de função que executam no Linux num plano de Consumo. Para estes planos de hospedagem, você precisa, em vez disso, usar live metrics stream para ver os registos em tempo quase real.

#### <a name="live-metrics-stream"></a>Live Metrics Stream

Pode ver o [Live Metrics Stream](../articles/azure-monitor/app/live-stream.md) para a sua aplicação de função numa nova janela do navegador, incluindo a `--browser` opção, como no exemplo seguinte:

```bash
func azure functionapp logstream <FunctionAppName> --browser
```
